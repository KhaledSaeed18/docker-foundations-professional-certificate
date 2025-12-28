# 01 – Exploring the Docker CLI

## What Is the Docker CLI?

The **Docker Command Line Interface (CLI)** is the primary way developers interact with Docker.

Using the CLI, you can:

- Run and stop containers
- Pull and manage images
- Create and manage networks
- Inspect Docker resources

The Docker CLI is designed to be **simple, consistent, and easy to explore**.

## Docker Command Structure

Docker commands generally follow this structure:

```bash
docker <command> <subcommand> [options]
```

Each command is composed of:

1. **Top-level command** – the main Docker feature
2. **Subcommand** (optional) – a specific action
3. **Options / flags** – modify command behavior

## Top-Level Commands

Top-level commands represent major Docker capabilities.

Examples include:

- docker run
- docker pull
- docker login
- docker network

Each top-level command groups related functionality.

## Subcommands

Some top-level commands provide subcommands for more granular operations.

Example:

- docker network create
- docker network connect
- docker network disconnect

Here:

- `network` is the top-level command
- `create`, `connect`, and `disconnect` are subcommands

## The --help Flag

The **--help** flag is the most important discovery tool in the Docker CLI.

### Key Characteristics

- Supported by every Docker command
- Displays usage instructions
- Lists available subcommands
- Shows supported options and flags

### Common Usage

- docker --help  
  Shows all available top-level Docker commands.

- docker network --help  
  Shows subcommands related to Docker networking.

- docker network create --help  
  Shows usage and options for creating a network.

## Usage Output Behavior

When a Docker command is run:

- With **--help**, full documentation is shown
- Without required arguments, a short usage message is displayed

This behavior encourages learning commands interactively.

## CLI Design Philosophy

The Docker CLI is:

- Self-documenting
- Consistent across commands
- Designed for exploration without memorization

Experienced users frequently rely on **--help** during daily work.

## Key Takeaway

If you are unsure how a Docker command works, append **--help** and explore its options.
