# 09 – Saving Data from Containers

Containers are designed to be **disposable**.  
When a container is removed, anything stored inside it (files, folders, generated data) is removed too.

This section shows:

- Why data “disappears” after containers stop
- How to persist data using **volume mounts** (`-v` / `--volume`)
- The difference between mounting directories vs mounting files
- A common pitfall: mounting a file that doesn’t exist

---

## Containers Are Ephemeral (Data Does Not Persist)

Anything created inside a container stays inside that container.

If the container is removed, the data is removed with it.

---

## Example: Creating a File Inside a Container (Then Losing It)

### Command

```bash
docker run --rm --entrypoint sh ubuntu -c "echo 'Hello there.' > /tmp/file && cat /tmp/file"
```

### Output

Unable to find image 'ubuntu:latest' locally  
latest: Pulling from library/ubuntu  
Digest: sha256:c35e29c9450151419d9448b0fd75374fec4fff364a27f176fb458d472dfc9e54  
Status: Downloaded newer image for ubuntu:latest  
Hello there.

### What Happened

- `--rm` removes the container after it exits
- `--entrypoint sh` overrides the image’s default entrypoint
- `-c "..."` runs a shell command:
  - writes “Hello there.” to `/tmp/file`
  - prints the file content

The file existed **inside the container**, not on your host.

---

## Trying to Access the File on the Host

### Command

```bash
cat /tmp/file
```

### Output

cat: /tmp/file: No such file or directory

### Why This Fails

`/tmp/file` was created in the container’s filesystem.  
Once the container exited (and `--rm` removed it), that filesystem (and the file) disappeared.

---

## Persisting Data with Volume Mounts

Docker can map:

- A folder on your host → to a folder inside the container
- A file on your host → to a file inside the container

This is called **volume mounting**.

### Flag

-v or --volume

### Format

-v <host_path>:<container_path>

Mnemonic:
**outside : inside**

- outside = host path
- inside = container path

---

## Mounting a Host Directory into the Container

### Command

```bash
docker run --rm --entrypoint sh -v /tmp/container:/tmp ubuntu -c "echo 'Hello there.' > /tmp/file && cat /tmp/file"
```

### Output

Hello there.

### What Changed

- Host folder: `/tmp/container`
- Container folder: `/tmp`

So when the container writes `/tmp/file`, it actually writes into:

- host path: `/tmp/container/file`

---

## Verifying the File Exists on the Host

### Command

```bash
cat /tmp/container/file
```

### Output

Hello there.

Now the file persists because it was written into a host-mounted folder.

---

## Mounting a Host File into the Container

You can mount a specific file too, not just a directory.

### Step 1: Create a Host File

#### Command

```bash
touch /tmp/change_this_file
```

---

### Step 2: Mount Host File → Container File

#### Command

```bash
docker run --rm --entrypoint sh -v /tmp/change_this_file:/tmp/file ubuntu -c "echo 'Hello there.' > /tmp/file && cat /tmp/file"
```

#### Output

Hello there.

### What Happened

- `/tmp/change_this_file` on the host is mounted as `/tmp/file` inside the container
- When the container writes to `/tmp/file`, it modifies the host file directly

---

## Important Caveat: Mounting a Non-Existent File

If you mount a host “file path” that does not exist, Docker treats it as a **directory**.

### Command

```bash
docker run --rm --entrypoint sh -v /tmp/this_file_does_not_exist:/tmp/file ubuntu -c "echo 'Hello there.' > /tmp/file && cat /tmp/file"
```

### Output

sh: 1: cannot create /tmp/file: Is a directory

### Why This Happens

- `/tmp/this_file_does_not_exist` does not exist on the host
- Docker creates it as a directory and mounts it
- Inside the container, `/tmp/file` becomes a directory
- Writing `> /tmp/file` fails because you can’t write a file “into” a directory path like that

---

## Key Takeaways

- Containers are disposable; their internal files vanish when they are removed.
- Use `-v host:container` to persist data outside the container.
- Directory mount example: `-v /tmp/container:/tmp`
- File mount example: `-v /tmp/change_this_file:/tmp/file`
- If the host “file” doesn’t exist, Docker mounts it as a directory — causing confusing errors.

Rule of thumb:
> If you mount files, make sure the host file already exists.
