import subprocess
import sys
import json

if len(sys.argv) < 2:
    print('\nUSAGE: docker run --gpus all -v $[path to script]:/script.py [OPTIONAL: -v $[path to args.json]:/args.json] dynamo-script:latest /path/to/prompts.jsonl [OPTIONAL: --dynamo-flags]\n')
    print('EXAMPLES:')
    print('docker run --gpus all -v $(pwd)/dynamo-run-script.py:/script.py dynamo-script:latest /data/prompts.jsonl')
    print('docker run --gpus all -v $(pwd)/dynamo-run-script.py:/script.py -v $(pwd)/args.json:/args.json dynamo-script:latest /data/prompts.jsonl --extra-engine-args args.json\n')
    sys.exit(1)

batch_path = sys.argv[1]  # path to JSONL with prompts (one {"text": "..."} per line)
extra_flags = " ".join(sys.argv[2:])  # Grab any additional flags after the path

# Check if '--extra-engine-args args.json' is in the flags
if "--extra-engine-args args.json" in extra_flags:
    # Read user-provided args.json
    with open("args.json", "r") as f:
        args_data = json.load(f)

# Base command: use the provided JSONL path as batch input
run_command = f"dynamo run in=batch:{batch_path} out=vllm Qwen/Qwen3-0.6B {extra_flags}"
print(run_command)

# Build the command, appending extra flags (if any)
command = f"""
source /venv/bin/activate
etcd &
nats-server -js &
{run_command}
"""

# Run the command and wait
process = subprocess.Popen(command, shell=True, executable="/bin/bash")
process.wait()

# Read and print the output
with open("output.jsonl", "r") as f:
    for line in f:
        output = json.loads(line)
        print(output)
