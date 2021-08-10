# Testing Azure Static Web Apps with yarn workspaces

The purpose of this repository is to test how Azure Static Web Apps handles deploying a monorepo built using yarn workspaces. This monorepo consists of the following three packages which can be found in the `packages` folder:

- `@aswa-yarntest/api` is an Azure Functions app that loads a string from its dependency `@aswa-yarntest/common`.
- `@aswa-yarntest/common` is a simple CommonJS module that exports a string which is consumed by `@aswa-yarntest/api`.
- `@aswa-yarntest/ui` is a simple create-react-app boilerplate. This package has no purpose in the test, aside from that Azure Static Web App (obviously) requires a web app frontend.

*Note:* in order to trigger Oryx to use yarn instead of npm (see [issue](https://github.com/microsoft/Oryx/issues/1073)), [a dummy lockfile](https://github.com/jeroenvdmeer/aswa-yarntest/blob/main/packages/api/yarn.lock) is added to `@aswa-yarntest/api`.

## Test steps
1. After cloning this project, execute `yarn install` to install the packages with its dependencies.
2. Navigate to the `api` folder: `cd packages/api`
3. Start the Functions app: `yarn start`
4. Open the API endpoint, which is likely to be http://localhost:7071/api/HttpTrigger1
5. Validate that the following text is shown: `This value is defined in @aswa-yarntest/common`

## Test results
Instead of successfully building and deploying the `@aswa-yarntest/api` Functions app, [deployment failed](https://github.com/jeroenvdmeer/aswa-yarntest/actions/runs/1115329601) because yarn tries to find `@aswa-yarntest/common` in the public registry. Instead yarn should have picked up that this package is defined in the folder `packages/common`.

```
Starting to build function app with Oryx
---Oryx build logs---


Operation performed by Microsoft Oryx, https://github.com/Microsoft/Oryx
You can report issues at https://github.com/Microsoft/Oryx/issues

Oryx Version: 0.2.20210410.1, Commit: e73613ae1fd73c809c00f357f8df91eb984e1158, ReleaseTagName: 20210410.1

Build Operation ID: |/5hcDTgpJWw=.872011ba_
Repository Commit : 4279df62dc0e5cd3af03a7401348cef2f3fa682e

Detecting platforms...
Detected following platforms:
  nodejs: 14.17.4


Source directory     : /github/workspace/packages/api
Destination directory: /bin/staticsites/ss-oryx/api


Using Node version:
v14.17.4

Using Yarn version:
1.22.10

Installing production dependencies in '/github/workspace/packages/api/.oryx_prod_node_modules'...

Running 'yarn install --prefer-offline --production'...

yarn install v1.22.10
warning package.json: No license field
warning ../package.json: No license field
warning @aswa-yarntest/api@1.0.0: No license field
[1/4] Resolving packages...
error An unexpected error occurred: "https://registry.yarnpkg.com/@aswa-yarntest%2fcommon: Not found".
info If you think this is a bug, please open a bug report with the information provided in "/github/workspace/packages/api/.oryx_prod_node_modules/yarn-error.log".
info Visit https://yarnpkg.com/en/docs/cli/install for documentation about this command.


---End of Oryx build logs---
Oryx has failed to build the solution.

For further information, please visit the Azure Static Web Apps documentation at https://docs.microsoft.com/en-us/azure/static-web-apps/
If you believe this behavior is unexpected, please raise a GitHub issue at https://github.com/azure/static-web-apps/issues/
Exiting
```