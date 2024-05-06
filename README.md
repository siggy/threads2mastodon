# threads2mastodon

Rough commands to find ActivityPub-enabled threads.net followings, and then follow them in Mastodon.

Download your threads.net data at:
https://accountscenter.instagram.com/info_and_permissions/dyi/

Retrieve your threads.net followings:
`./your_instagram_activity/threads/following.json`

```bash
docker run -it python /bin/bash

apt-get update && apt-get install jq

pip install toot
toot login

# NOTE: watch for "Error: Too many requests" failures. You may have break up
# following.json into smaller files.
cat following.json |
  jq -r .text_post_app_text_post_app_following[].string_list_data[].value |
  sort |
  xargs -I {} bash -c '
    response=$(
      toot search --json "@{}@threads.net" |
      jq -r ".accounts[0].acct // empty"
    ) &&
    if [ -z "$response" ]; then
      echo "{} is not ActivityPub enabled";
    else
      toot follow "@{}@threads.net";
    fi'
```
