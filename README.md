# Testing Azure Static Web Apps with yarn workspaces

The purpose of this repository is to test how Azure Static Web Apps handles deploying a monorepo built using yarn workspaces. This monorepo consists of the following three packages which can be found in the `packages` folder:

- `@aswa-yarntest/api` is an Azure Functions app that loads a string from its dependency `@aswa-yarntest/common`.
- `@aswa-yarntest/common` is a simple CommonJS module that exports a string which is consumed by `@aswa-yarntest/api`.
- `@aswa-yarntest/ui` is a simple create-react-app boilerplate. This package has no purpose in the test, aside from that Azure Static Web App (obviously) requires a web app frontend.

## Test steps
1. After cloning this project, execute `yarn install` to install the packages with its dependencies.
2. Navigate to the `api` folder: `cd packages/api`
3. Start the Functions app: `yarn start`
4. Open the API endpoint, which is likely to be http://localhost:7071/api/HttpTrigger1
5. Validate that the following text is shown: `This value is defined in @aswa-yarntest/common`
