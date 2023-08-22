<!-- 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 -->

# publishing docker hub images

### In this documentation i'm going to show how we to publish docker image on docker.

to publish docker image on github Follow these steps:

Generate Secret Token
Login to your git hub account and got to ```settings < Developer Settings < Personal and access token < Tokens (classic) < Generate New Token < Generate New Token (Classic)```

![Alt text](image.png)

Enter note of token and set Expiration Days and Make sure to check boxes which selected in image below.

![Alt text](image-1.png)

Scroll Down and Click on 

![Alt text](image-2.png)

You got on this page make sure to copy Token before reloading page else you lost it and to get again this token have to update token so make sure to copy first and save somewhere.

![Alt text](image-3.png)

Make sure to copy your personal access token now. You won’t be able to see it again! 
copy token by clicking on copy (blue) button beside token
![Alt text](image-4.png)
```
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
```
## Get started with code
### Using terminal 
open terminal and start with the commands
```
touch helloWorld.sh 
```
```
vim ./helloWorld.sh
```
Paste this Code in the file
```
#!/bin/bash

echo "Hello World!"
```
New create Dockerfile
```
touch ./dockerfile 
```
```
vim ./dockerfile
```
Paste this code in the file 
```
FROM alpine

RUN apk add --no-cache bash

WORKDIR /app

COPY helloWorld.sh .
RUN chmod +x helloWorld.sh 

ENTRYPOINT [ "/app/helloWorld.sh" ]
```
save and close file

### Lets publish our docker image in github
Open Terminal by pressing ```Alt + Ctrl + t```

#### First we have to login on ghcr.io

docker login --username YOUR_USER_NAME --password ENTER_TOKEN
```
docker login --username bugsfounder --password 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
```
Now Lets Build the image. (Make sure to change username i'm using bugsfounder change it to yours)

-t --> tag
```
docker build -t ghcr.io/bugsfounder/hello-world-ghcr:latest
```
Let's Push the image on ghcr
```
docker push ghcr.io/bugsfounder/hello-world-ghcr:latest
```
Check Local iamges
![Alt text](image-5.png)
Run the image 
```
docker run ghcr.io/bugsfounder/hello-world-ghcr:latest
```
![Alt text](image-6.png)

Let's Delete local image 
![Alt text](image-7.png)

Now Lets run the image again
```
docker run ghcr.io/bugsfounder/hello-world-ghcr:latest
```
```terminal 
bugs@bugs-ThinkPad-T460s:~$ docker run ghcr.io/bugsfounder/hello-world-ghcr:latest
Unable to find image 'ghcr.io/bugsfounder/hello-world-ghcr:latest' locally
latest: Pulling from bugsfounder/hello-world-ghcr
8a49fdb3b6a5: Already exists 
1fa0aab3c125: Already exists 
23243d141af5: Already exists 
223d15820423: Already exists 
63ccb926b07e: Already exists 
Digest: sha256:0ce0062351448adf875858776b9fb938981e0e57a3ebc13fa9f5fc2195727111
Status: Downloaded newer image for ghcr.io/bugsfounder/hello-world-ghcr:latest
Hello world!
```

New Lets add workflow so we change code in our repo it automatically update the docker image.

push all code on a git repo and we have to create folders in repository, 

Go to the ```Settings``` of repo and then ```Secrets and variables < Actions < New Repository Secret```
![Alt text](image-9.png)
```
touch  ./.github/workflows/
```
Enter Name of secret and remember. in this example i'm taking ```GT_SEC```
put Token that we created before in the ```Secret*``` Box.
Now create a file called ```publish-ghcr.yaml``` under ```./.github/workflows/publish-ghcr.yaml```

write code in ```publish-ghcr.yaml``` file. make sure to change ```bugsfounder``` to your username and ```GT_SEC``` to YOUR_SECRET_NAME
```yaml
# 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
name: Docker Image CI for GHCR

on:
  push

jobs:
  build_and_publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build and push the image
        run: |
          docker login --username bugsfounder --password ${{ secrets.GT_SEC }} ghcr.io
          docker build . --tag ghcr.io/bugsfounder/hello-world-ghcr:latest
          docker push ghcr.io/bugsfounder/hello-world-ghcr:latest
```

Now commit the code and push code on git hub, Go to your profile's Packages section you can see docker images.
![Alt text](image-10.png)

You can simply use this images on any system using command below
```
docker run ghcr.io/bugsfounder/hello-world-ghcr:latest
```

## If you are getting
```
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Error response from daemon: Get "https://registry-1.docker.io/v2/": unauthorized: incorrect username or password
```
## Use below command to make it work
```
# echo "YOUR_PERSONAL_ACCESS_TOKEN" | docker login ghcr.io -u USERNAME --password-stdin
echo "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" | docker login ghcr.io -u bugsfounder --password-stdin
```
