# How to run admin locally (includes instructions for Mac M1 users)

Note: Original links and screenshots from the source company have been removed to protect their privacy.

(Updated: Dec 2, 2021, Generosa Litton)

This document describes how to run admin in your laptop so that you can make pull requests or contribute to Gatsby's source code

## Prerequisites and Set-up

- Access to the [XXX](https://www.npmjs.com/org/xxx) in [npmjs.com](https://www.notion.so/xxx)
- A clone of the mansion repo [https://github.com/xxx(https://github.com/XXX)
- [Docker for Mac](https://docs.docker.com/desktop/mac/install/), here's a link to [Docker for Mac M1](https://docs.docker.com/desktop/mac/apple-silicon/)
- Use at least [node version 14](https://nodejs.org/en/)
- Additionally it would be helpful to add your npmjs access token in your .zshrc file.

To get your npmjs token, log in to to [npmjs.com](http://npmjs.com) and click the dropdown link in your account name, then click Access Tokens.

![Screen Shot 2021-09-20 at 5.10.42 PM.png](XXX)

---

Click on Generate New Token.

![Screen Shot 2021-09-20 at 5.14.26 PM.png](XXX)

Copy the token and add it to your `.zshrc` or `.bashrc` file.

Also add the following puppeteer configuration values to make sure yarn install works.

```bash
#NPM Token for @gatsby-cloud-pkg
export NPM_TOKEN="<Your token>"

# Disable Chromium Download
export PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true
export PUPPETEER_EXECUTABLE_PATH=`which chromium`

```

```bash
#NPM Token for @gatsby-cloud-pkg
export NPM_TOKEN="<Your token>"

# Disable Chromium Download
export PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true
export PUPPETEER_EXECUTABLE_PATH=`which chromium`

```

Save your file and restart your terminal

## Steps to Run Admin Locally

To get admin up and running, you will need to run three services in three separate terminal windows.

- In the first terminal window, you will run mansion/cloud locally.
- Second, you will run the Gatsby Cloud dashboard in another terminal and get your site content added.
- In the third terminal window, you will be running admin.

### Get mansion/cloud up and running

1. cd into the cloud directory

```bash
cd cloud
```

1. For Mac M1 users, do [this](https://github.com/XXX) first which is create a .env file in the cloud root directory and add the following:

```bash
export ELASTIC_SEARCH_VERSION="7.8.0-arm64"
export PRISMA_IMAGE="sidhartha/prisma"
```

1. Install all dependencies first using yarn install (described [here](https://github.com/XXX)) before running docker-compose up -d
2. Start docker for Mac and run docker-compose up -d
3. Once you have docker and all the dependencies installed, go to the service/dev-monolith directory.

```bash
cd services/dev-monolith
```

1. Run `yarn install`. You might get an error that looks like

```
No receipt for 'com.apple.pkg.CLTools_Executables' found at '/'.

No receipt for 'com.apple.pkg.DeveloperToolsCLILeo' found at '/'.

No receipt for 'com.apple.pkg.DeveloperToolsCLI' found at '/'.

gyp: No Xcode or CLT version detected!
gyp ERR! configure error
gyp ERR! stack Error: `gyp` failed with exit code: 1
gyp ERR! stack     at ChildProcess.onCpExit (/Users/glitton/mansion/cloud/node_modules/node-gyp/lib/configure.js:351:16)
gyp ERR! stack     at ChildProcess.emit (events.js:314:20)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:276:12)
gyp ERR! System Darwin 20.4.0
gyp ERR! command \"/Users/glitton/.nvm/versions/node/v12.22.1/bin/node\" \"/Users/glitton/mansion/cloud/node_modules/.bin/node-gyp\" \"rebuild\"
gyp ERR! cwd /Users/glitton/mansion/cloud/node_modules/sse4_crc32
gyp ERR! node -v v12.22.1
gyp ERR! node-gyp -v v5.0.7
gyp ERR! not ok"
info This module is OPTIONAL, you can safely ignore this error
```

If you get the above, you will need to re-install Xcode. Check out this [stackoverflow thread](https://stackoverflow.com/questions/34617452/how-to-update-xcode-from-command-line) for how to get past this Xcode error. What worked for me was doing:

```
sudo rm -rf /Library/Developer/CommandLineTools
xcode-select --install
```

After re-installing, run `yarn install` again.

1.  Navigate to the root of mansion/cloud. Run `yarn reset-env`. This step takes a few minutes so go get a glass of water, tea or coffee 😀. Your terminal output should be similar to the screenshot below.

![Screen Shot 2021-09-20 at 5.42.09 PM.png](XXX)

1. Navigate to the services/dev-monolith directory and start the service. Note that when you try to run other services in the two terminal windows, you may get a memory error. To get past this, it is best to add NODE_OPTIONS in the yarn start command.

```bash
cd services/dev-monolith
NODE_OPTIONS=--max-old-space-size=16000 yarn start:local:watch
```

You know you are in good shape when you see terminal logs like:

```bash
Loading Monolith Vars
Loading Systems Platform Local Environment Variables
Loading Lighthouse Local Environment Variables
Loading Captain Local Environment Variables
Loading CDN Local Environment Variables
Loading Usage Local Environment Variables
Loading Auth Local Environment Variables
Loading Core Local Environment Variables
Loading Function Manager Environment Variables
Loading Local Gateway Environment Variables
Loading Communications Local Environment Variables
Loading Payments Local Environment Variables
Loading Reporter Local Environment Variables
Loading Metrics Local Environment Variables
Loading Analytics Local Environment Variables
Loading cloud-flags local environment variables
Loading cloud init local environment variables
Loading versions local environment variables
Loading Eventador Local Environment Variables
```

You will likely see this Salesforce error which you shouldn't worry about. If you reached this step, you are now running mansion locally!

```bash
error: Failed to login Salesforce grant type not supported {
  name: 'unsupported_grant_type',
  stack: 'unsupported_grant_type: grant type not supported\n' +
    '    at /Users/glitton/mansion/cloud/node_modules/jsforce/lib/oauth2.js:198:19\n' +
    '    at tryCallOne (/Users/glitton/mansion/cloud/node_modules/promise/lib/core.js:37:12)\n' +
    '    at /Users/glitton/mansion/cloud/node_modules/promise/lib/core.js:123:15\n' +
    '    at flush (/Users/glitton/mansion/cloud/node_modules/asap/raw.js:50:29)\n' +
    '    at processTicksAndRejections (internal/process/task_queues.js:79:11)'
}
```

1.  To make sure you are running mansion locally, go to `[localhost:8083/core/graphql](http://localhost:8083/core/graphql)` and see if you can run queries.

### Add a Site To the Gatsby Cloud Dashboard

1. Open another terminal window and navigate to `cloud/gatsbyjs.com`
2. Run `yarn start:dashboard`
3. When the build completes, go to the go the Gatsby Cloud dashboard at [`http://localhost:8000/`](http://localhost:8000/) and sign up to Github or whatever VCS provider you use.
4. You can select a standard plan and use a dummy stripe credit card which has a number 4242 4242 4242 4242. Add in a future expiration date, a CVC number, and an address.
5. Add any gatsby site that you have access to. This site is needed so you will have content to search for while running admin locally.

### Run Gatsby Admin Locally

1. In another terminal window, navigate to `cloud/services/XXX`
2. Run `yarn start:local`
3. You should be able to view admin locally at [http://localhost:8001/](http://localhost:8001/)

### Work on your features and remember to NOT commit the docker-compose.yaml file changes that you made for the MAC M1!

## What If i get a `dyld[26445]: missing symbol called` error?

On an M1 mac, you have to download the Node that uses Arm instead of Intel.

1. Uninstall any version of Node 14.17 and Node 16 install
2. Run `arch -x86_64 bash`
3. In that new terminal `nvm install 14.17` and `nvm install 16`
4. Exit and open a new terminal
5. `nvm use 14.17` to switch

Reference : [https://www.jurnalanas.com/node-js-mac-m1/](https://www.jurnalanas.com/node-js-mac-m1/)
