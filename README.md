## Using Docker Compose in a Codespace

### Step 1. Check the docker-compose configuration works inside the default/universal Codespace without a devcontainer.json.

Run `$ docker-compose up`

If this works we can go on to configure a Dev Container.

### Step 2. Add a Docker Compose configuration that will be used by the VS Code editor.

Add a new file `.devcontainer/docker-compose.yml`.

```yaml
version: '3'
services:
   code:
    # See https://github.com/devcontainers/images
    image: mcr.microsoft.com/devcontainers/universal:linux

    # Don't shut down after the process ends.
    command: sleep infinity
```

The `code` service will be used by VS Code and must contain Git and whatever languages are required by its extensions (e.g. php if you want language support when editing php files).

The `command: sleep infinity` is required if the image exits. This isn't nessesary when using `universal:linux` but `base:alpine` will fail without this option.

### Step 3. Add a Dev Container to run Docker Compose

 Add a new `.devcontainer/devcontainer.json` file.

```json
 {
	"name": "Existing Docker Compose",

	"dockerComposeFile": [
		"../docker-compose.yml",
		"docker-compose.yml"
	],

	"service": "code",

	"workspaceFolder": "/workspaces/${localWorkspaceFolderBasename}",

	"hostRequirements": {
	   "cpus": 4
	},

	// Use 'forwardPorts' to make a list of ports inside the container available locally.
	"forwardPorts": [5000, 5001]
}
```

`dockerComposeFile` points to our original `../docker-compose.yml` file and the new `docker-compose.yml` which contains the `code` service that will be used by VS Code.

`service` specifies the `code` service whoch will be used by VS Code.

`workspaceFolder` points to the base of our repo. This should be optional, but ugly warnings appear if it's missing.

`hostRequirements` ensures we launch a codespace large enough for all our services.

`forwardPorts` specifies the ports our services expose and should be accessible via the `Ports` tab. 
