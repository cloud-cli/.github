# Cloudy

## What is Cloudy?

Cloudy is a Node.JS project that works both as an API and a CLI tool.
It can be used to automate the deploy and configuration of apps in a remote/cloud machine.

Everything you can run, apart from the CLI itself, is a separated module.

## Concepts

We start with a blank machine, say Ubuntu 20+.

Then we install and configure our CLI, both on localhost and the remote machine.

Now you have a local tool that can run commands on your server, but no commands available yet.
New commands are provided by installable modules.

For example:

- You installed Cloudy on your local machine and your server (e.g. your phone, with Termux, and an instance on a cloud provider).
- You install the `@cloud-cli/sys` module on your server and start it (running `cy --serve`).
- Now you can add modules and expanding your features, e.g. `cy sys.install px` to have a reverse proxy, or `cy sys.install le` for Let's Encrypt cert generation.
- The add-ons are loaded by Cloudy automatically next time you run `cy --serve`.

Available modules:

- [Local DNS names](https://github.com/cloud-cli/dns)
- [Local Proxy](https://github.com/cloud-cli/px)
- [Let's Encrypt](https://github.com/cloud-cli/le)
- [GitHub WebHooks](https://github.com/cloud-cli/gd)
- [Environment variables](https://github.com/cloud-cli/env)
- [SMB Mounts](https://github.com/cloud-cli/smb)
- [Docker Volumes](https://github.com/cloud-cli/vm)
- [Local Docker Registry](https://github.com/cloud-cli/dr)
- [Run Docker Containers](https://github.com/cloud-cli/dx)


### Examples

On the server:

```bash
# install Cloudy CLI
npm i -g @cloud-cli/cli

# generate a random key
head -c 5000 /dev/urandom | sha512sum

# copy the hash from above
echo '[paste-the-generated-key-here]' > key
```

```ts
// cloudy.conf.mjs

import { init } from '@cloud-cli/cli';
export default {
  [init]() {
    // anything you need to run when the http server starts
  }
};

// optional, change remote port. Default is 1234
export const apiPort = 8844;

// optional, change listening host. Default is localhost (no remote access)
export const apiHost = '0.0.0.0';
```

And then we start the Cloudy server:

```bash
cy --serve
```

On your local environment

```bash
npm i -g @cloud-cli/cli
```

```ts
// cloudy.conf.mjs
// same port as the config on the server
export const apiPort = 8844;
export const remoteHost = 'your-server.com';
export const key = '[paste-the-generated-key-here]';
```

Now you can run `cy --help` to get a list of commands available.

## How it works

- A call to `cy foo.bar --option value` on localhost is converted to a POST request to `your-server.com/foo.bar` with a JSON body `{ "option": "value" }`.

- The server runs your command and returns an output

- The same commands can be executed with the `cy` tool inside the server and in your local machine.

- And if you need to run the same but from a browser, the entire CLI is also an API!
