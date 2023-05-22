# Cloudy

## What is Cloudy?

Cloudy is a Node.JS project that works both as an API and a CLI tool.
It can be used to automate the deploy and configuration of apps in a remote/cloud machine.

Everything you can run, apart from the CLI itself, is a separated module.

## Concepts

We start with a new machine running GNU/Linux. We will call it the `server`.
We configure our server to accept HTTP(S) requests and protect it with a secret string.
We run a command to start a server in this machine.

Then we execute commands from anywhere: be it a Linux machine, macOS or a browser.
We call this our `client`.

Then we install modules on the server side to add features and commands.

For example:

- You install Cloudy on your local machine and your server (e.g. your phone, with Termux, and an instance on a cloud provider).
- You install the `@cloud-cli/sys` module on your server.
- You add a few modules, like `cy sys.install px` for a reverse proxy, and `cy sys.install le` for Let's Encrypt certificate creation.
- You start the server with `cy --serve`

> Note: some modules require additional programs to be present on the server, e.g. `le` requires `certbot` to be present.

## Available modules

| name | features |
| - | - |
| [dns](https://github.com/cloud-cli/dns) | Local DNS resolution |
| [px](https://github.com/cloud-cli/px) | Reverse Proxy |
| [le](https://github.com/cloud-cli/le) | Let's Encrypt |
| [gd](https://github.com/cloud-cli/gd) | GitHub WebHooks |
| [env](https://github.com/cloud-cli/env) | Environment variables |
| [smb](https://github.com/cloud-cli/smb) | SMB Mounts |
| [vm](https://github.com/cloud-cli/vm) | Docker Volumes |
| [dr](https://github.com/cloud-cli/dr) | Local Docker Registry |
| [dx](https://github.com/cloud-cli/dx) | Run Docker Containers |

## Step-by-step example

On the `server`:

```bash
# install Cloudy CLI
npm i -g @cloud-cli/cli

# generate a random secret
head -c 5000 /dev/urandom | sha512sum | cut -d' ' -f 1
```

Now open `cloudy.conf.mjs` and configure the server:

```ts
// cloudy.conf.mjs
export const key = '[secret generated above]';
```

And then we start the Cloudy server:

```bash
cy --serve
```

On your `client` side:

```bash
npm i -g @cloud-cli/cli
```

```ts
// cloudy.conf.mjs
export const key = '[secret generated above]';
```

Now you can run `cy --help` to get a list of commands available.

## How it works and the HTTP API

- A call to `cy foo.bar --option value` on the `client` is converted to a POST request to `your-server.com/foo.bar` with a JSON body `{ "option": "value" }`.

- The server runs your command and returns a JSON output, which is then printed back.
The request looks like this:

```
POST /foo.bar
Host: your-server.com
Authorization: Bearer [cloudy-secret]

{ "option": "value" }
```

## Additional settings

```ts
import { init } from '@cloud-cli/cli';

// optional, any code that you need to run when the server starts
export default {
  [init]() {
    // ...
  }
};

// optional, change remote port. Default is 1234.
export const apiPort = 8844;

// optional, change listening host. Default is 127.0.0.1 (no remote access!)
export const apiHost = '0.0.0.0';

```
