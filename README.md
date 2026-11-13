# opencode-agents

OpenCode configuration for one penetration-testing agent, and a skill note for the Docker MCP server that agent is set up to call.

## What it is and why

`pentest-agent/opencode.json` is an OpenCode config file. Its `$schema` is `https://opencode.ai/config.json`. The file does two things:

- It registers a local MCP server named `docker-mcp-manager`.
- It registers a primary agent named `pentest-agent`.

The agent prompt is `pentest-agent/pentest-agent.md`. That prompt tells the agent to run a penetration test against a target written in the same file, to use the Docker MCP manager, and to record actions in a report. The model set on the agent is `nvidia/nemotron-3-super-120b-a12b:free`.

`pentest-agent/docker-mcp-manager/SKILL.md` describes tools for running commands and reading or writing files in a Docker container with persistent storage mounted at `/data`. The server program is not in this repository.

## Prerequisites

- OpenCode, because the config uses the OpenCode config schema.
- Python 3 available as `python3`, because that is the MCP server command.
- The MCP server script named in `pentest-agent/opencode.json`. This checkout does not contain it.
- Docker, because the skill describes a managed Docker container. This repository has no Dockerfile or Compose file.

## Install, build, run, and test

This repository has no package manifest, install script, build script, or test suite.

Check that the config is valid JSON:

```bash
python3 -m json.tool pentest-agent/opencode.json
```

The MCP server command recorded in `pentest-agent/opencode.json` is:

```bash
python3 /home/user/Projects/mcp/docker-mcp-manager/docker_mcp_server.py
```

That path is outside this repository. From this checkout the command fails because the script is not here.

## Usage

The config loads the agent prompt from this path:

```text
{file:~/.config/opencode/agents/pentest-agent.md}
```

Copy the prompt there so that reference can resolve:

```bash
mkdir -p ~/.config/opencode/agents
cp pentest-agent/pentest-agent.md ~/.config/opencode/agents/pentest-agent.md
```

`pentest-agent/pentest-agent.md` hardcodes a target host and objectives. Read that file and replace the target before use. This README does not repeat the address.

The agent frontmatter sets `mode: primary` and enables `read`, `write`, `edit`, `grep`, `glob`, `bash`, `question`, and `webfetch`. In `pentest-agent/opencode.json`, agent and global permissions default to `ask`. Bash allows `ls`, `grep`, `cat`, `find`, `pwd`, `cd`, `ps`, and `echo`. Bash asks before `git`, `chmod`, `chown`, and `kill`. Bash denies `rm` and `sudo`. Read, edit, and write deny `.env` files and common key and certificate filenames. `external_directory` allows `~/projects/personal/**` and `~/documents/**`, and asks for `~/downloads/**`.

The skill file names these tools: `execute_command`, `read_file`, `write_file`, `list_directory`, `get_container_info`, and `check_file_exists`. Their implementation is not in this repository.

## Layout

```text
pentest-agent/opencode.json
pentest-agent/pentest-agent.md
pentest-agent/docker-mcp-manager/SKILL.md
```

## License

`pentest-agent/docker-mcp-manager/SKILL.md` sets `license: MIT` in its frontmatter. This repository has no `LICENSE` file.
