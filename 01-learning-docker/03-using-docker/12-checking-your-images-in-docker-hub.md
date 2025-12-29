# 12 – Checking Your Images in Docker Hub

After pushing images to Docker Hub, it’s important to verify that they were uploaded correctly and understand how tags appear and behave in the registry.

---

## Viewing Images in Docker Hub

Once logged into Docker Hub:

- You’ll see a list of repositories under your account
- Each repository represents an image you’ve pushed

If the push was successful:

- Your image appears immediately in the list
- This confirms the image is available in the registry

---

## Inspecting an Image Repository

Clicking on an image repository shows:

- All available **tags** (versions)
- The operating system / architecture compatibility
- The last time each tag was pushed

If many tags exist, you may need to click **“See all tags”** to view them.

---

## Pushing a New Version (New Tag)

You can push the same image again using a **different tag**.

Example workflow:

- Tag the image with a new version (e.g. `0.0.2`)
- Push the new tag to Docker Hub

Because Docker images are built from layers:

- Docker detects layers that already exist
- Only **new or changed layers** are uploaded
- If nothing changed, the push completes almost instantly

After refreshing Docker Hub:

- The new tag appears alongside older tags

---

## Deleting an Image Repository

If an image is no longer needed:

- It can be deleted from Docker Hub **via the web interface**
- There is no simple CLI command for deleting repositories

To delete:

1. Open the image repository
2. Go to **Settings**
3. Choose **Delete repository**
4. Enter the **repository name**
5. Confirm deletion

Once deleted:

- All tags under that repository are removed
- The image disappears from your Docker Hub account

---

## Key Takeaways

- Pushed images appear immediately in Docker Hub
- Tags represent versions of the same image
- Pushing a new tag does not re-upload unchanged layers
- Image repositories must be deleted from the web UI
- Deleting a repository removes all its tags
