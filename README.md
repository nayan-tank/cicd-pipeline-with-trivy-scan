
# What is Trivy ? 

Trivy is an open-source vulnerability scanner primarily used in containerized environments like Docker and Kubernetes. It analyzes container images and applications to detect known vulnerabilities in their software components. By comparing installed software versions against vulnerability databases, Trivy generates reports that highlight identified vulnerabilities, including severity levels and additional information. Integrating Trivy into development and deployment processes helps identify security risks early on, enabling proactive patching and enhancing overall container security.

### Installation: ###

Trivy can be installed on various platforms, including Linux, macOS, and Windows. Refer to the Trivy documentation for the latest installation instructions: [Installation -  Trivy (aquasecurity.github.io)](https://aquasecurity.github.io/trivy/v0.41/getting-started/installation/)

### Scanning Container Images: ###

Run Trivy against a container image by executing the following command:

```
trivy image <IMAGE_NAME>
# or
trivy i <IMAGE_NAME>
```

Replace `<IMAGE_NAME>` with the name or URL of the container image you want to scan. Trivy will automatically download the necessary vulnerability database.

Scan a container image from a tar archive
```
trivy image --input <TAR_FILE_PATH>
```
![demo](https://miro.medium.com/v2/resize:fit:770/1*oZB2P5xqJLYwexZ2wX3TRg.png)

As you can see, the below nginx image(nginx:alpine) has a total of 6 vulnerabilities with Severity of (Critical, Medium, and High).

If you only want to see vulnerability with Severity of type Critical

![demo2](https://miro.medium.com/v2/resize:fit:770/1*ThCQOpFcg88cXcoszomZsA.png)

## Scanning git repository ##
```
# scan repo
trivy repo <REPO_URL>

# specific branch
trivy repo --branch <BRANCH_NAME> <REPO_URL>

# scan specific commit
trivy repo --commit <COMMIT_ID> <REPO_URL>

# scan specific version if have
trivy repo --tag <VERSION> <REPO_URL>

# scan private repo
export GITHUB_TOKEN=<TOKEN>
trivy repo <PVT_REPO_URL>

# use scanners
trivy repo --scanners vuln,secret,config <REPO_URL>
```

## Discover security misconfigurations ##

In the belove manifest, we have purposefully set the privileged attribute within the securityContext to true. Unfortunately, that is also security malpractice and should be avoided.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        securityContext:
          privileged: true
```

Now, let’s try to run the scan and see what Trivy tells us. To do so, use the following command:

```
trivy config <config_dir>
```

![demo3](https://miro.medium.com/v2/resize:fit:770/1*NAYed_ZHc-O6-zj-gLqtEA.gif)

And as we see, we get the listed set of vulnerabilities within the configuration. I need to point out that it has been detected that we have the root user within the Dockerfile, which is not a security best practice. We are also trying to run the Kubernetes deployment in privileged mode, and it has detected that.

## Conclusion ##

Trivy works quite well as a comprehensive static security analysis tool for container images and related configuration, and it gels well with your CI pipelines. If implemented effectively, it can greatly enhance your security posture.
