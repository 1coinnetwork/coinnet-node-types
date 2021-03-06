# coinnet-node-types

## Usage

This repo contains Typescript bindings for custom coinnet-node modules.

In order to use the standard API against 1COIN Network you must initialize the API's options object as follows:

```
import { spec } from '@1coinnetwork/node-types';

const options: ApiOptions = {
  provider : new WsProvider('ws://localhost:9944'),
  ...spec,
};

const api = new ApiPromise(options);
```

You will also need to update the `tsconfig.json` of your project to include the following:

```
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@polkadot/api/augment": ["./node_modules/@1coinnetwork/node-types/interfaces/augment-api.d.ts"],
      "@polkadot/types/augment": ["./node_modules/@1coinnetwork/node-types/interfaces/augment-types.d.ts"],
    }
  }
}
```

### Dependencies

Your project's @polkadot-js dependency versions **must match the versions used in this project**, including `@polkadot/api`, `@polkadot/keyring`, and all associated packages, as well as `rxjs` (if using `ApiRx` instead of `ApiPromise`) and `typescript`. To verify the versions required, inspect the @polkadot package.json files for the version specified in this project's package.json.

## Building

This project depends on the [@polkadot/typegen](https://github.com/polkadot-js/api/tree/master/docs/examples/promise/90_typegen) project, which has a step-by-step guide to building this project.

The 1COIN-specific version is as follows:

1. Ensure in [package.json](package.json) that `@polkadot/api` is set to the correct version, and that `@polkadot/typegen` is set to the same version as `@polkadot/api`. Increase the `@1coinnetwork/node-types` version if necessary for publication.

1. Ensure that all the `definitions.ts` files in `src/interfaces` are updated to the latest versions of each type, if any 1coin network modules changed.

1. Run an [coinnet-node](https://github.com/1coinnetwork/coinnet-node) chain on localhost.

1. You will notice that the output of the lint command contains an error. This is because the typegen script does not handle relative paths well for custom modules that depend on each other. You will have to make a manual change to [signaling/types.ts](src/interfaces/signaling/types.ts) as follows:

   ```diff
   - import { VoteStage } from './voting';
   + import { VoteStage } from '../voting';
   ```

1. Upgrade all [spec files](src/spec) with any changes necessary as per the [@polkadot-js changelog](https://github.com/polkadot-js/api/blob/master/CHANGELOG.md) and the underlying Substrate version of the chain.

1. To compile the Typescript to Javascript for npm publication, run `yarn build`. This command should not produce any errors and will output build files to the [dist](dist/) directory.

1. Run a simple test of the newly-built API against a live coinnet node with `yarn verify [mainnet | testnet]`.

1. Publish the new version with `npm publish`.
