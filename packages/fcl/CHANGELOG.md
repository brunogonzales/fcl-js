## 1.0.0-alpha.1

### Minor Changes

- 21a2dda: Import Buffer from rlp in encode-account-proof

### Major Changes

- Release 1.0.0 alpha

### Patch Changes

- Updated dependencies [7469c5c3]
- Updated dependencies

  - @onflow/util-address@1.0.0-alpha.0
  - @onflow/util-invariant@1.0.0-alpha.0
  - @onflow/rlp@1.0.0-alpha.0
  - @onflow/sdk@1.0.0-alpha.0
  - @onflow/types@1.0.0-alpha.0
  - @onflow/util-actor@1.0.0-alpha.0
  - @onflow/util-template@1.0.0-alpha.0
  - @onflow/util-uid@1.0.0-alpha.0

- 2022-03-28 -- [@JeffreyDoyle](https://github.com/JeffreyDoyle) Use latest SDK methods in fcl events subscriber.
- 2022-02-11 -- [@JeffreyDoyle](https://github.com/JeffreyDoyle) Uses Buffer from @onflow/rlp.
- 2022-02-25 -- [gregsantos](https://github.com/gregsantos): Remove `"FCL:LAUNCH:EXTENSION"` type from extension util send
- 2022-02-14 -- [gregsantos](https://github.com/gregsantos): Update `WalletUtils` encoding for authn-signing and auth-verifying.
  Encoding should leave all field that can exist as Buffers before RLP encoding as Buffers. This strategy will help maintain greater consistency with how these fields are treated when encoded in other areas of Flow.

```js
MESSAGE = HEX(
  USER_DOMAIN_TAG, // Buffer, right padded to 32 bytes long
  RLP(
    APP_DOMAIN_TAG, // [Optional] Buffer, right padded to 32 bytes long
    ADDRESS, // Buffer, left padded to 8 bytes long
    TIMESTAMP // Number
  )
)
```

- 2022-02-14 -- [chasefleming](https://github.com/chasefleming): Remove experimental redir warning from previous alpha build.
- 2022-02-08 -- [gregsantos](https://github.com/gregsantos): Update extension strategy to add support for `EXT/RPC` service method

## 0.0.79-alpha.3 - 2022-02-03

- 2022-02-03 -- [gregsantos](https://github.com/gregsantos): VSN `@onflow/sdk` 0.0.57-alpha.2 -> 0.0.57-alpha.3
- 2022-02-03 -- [gregsantos](https://github.com/gregsantos): Export `voucherIntercept` and `voucherToTxId` from `sdk`
- 2022-02-02 -- [gregsantos](https://github.com/gregsantos): Update start script to set current `VERSION`

## 0.0.79-alpha.2 - 2022-01-21

- 2022-01-21 -- [@JeffreyDoyle](https://github.com/JeffreyDoyle): VSN `@onflow/sdk` 0.0.57-alpha.1 -> 0.0.57-alpha.2

## 0.0.79-alpha.1 - 2022-01-21

- 2022-01-21 -- [@JeffreyDoyle](https://github.com/JeffreyDoyle): VSN `@onflow/sdk` 0.0.56 -> 0.0.57-alpha.1

## 0.0.78-alpha.11 - 2022-01-19

- 2022-01-19 -- Expose new `block` method from `@onflow/sdk`.
- 2022-01-19 -- VSN `@onflow/sdk` 0.0.56-alpha.2 -> 0.0.56-alpha.3
- 2022-01-18 -- Support `discovery.wallet.method` options when set in config for authentication with Discovery API services.
- 2022-01-18 -- Add missing `service` and `app` to config for exec service redirect.

## 0.0.78-alpha.10 - 2022-01-05

- 2021-12-17 -- Fix bug where `currentUser` passed as an argument is failing.
- 2021-12-02 -- Adds optional Authentication Refresh Service to `fcl.authenticate`

Wallet Providers can now provide an optional **Authentication Refresh Service** to FCL upon initial configuration.
If provided, FCL will attempt to refresh the user's session by executing this service before a transaction.

FCL will use the service endpoint and method provided to request updated authentication data from the Wallet Provider.
The service is expected to reauthenticate the user or prompt for approval if required. Updated authentication information
(user data and services) should be sent back to FCL as part of an APPROVED `PollingResponse`. (see example below using `WalletUtils.approve`)

If FCL receives back an APPROVED `PollingResponse`, it rebuilds `fcl.currentUser` with updated session data and services
and the Authentication Refresh process is complete.
The initial transaction can then be executed with confidence the user session is valid.

```javascript
import {WalletUtils} from "@onflow/fcl"

WalletUtils.approve({
  f_type: "AuthnResponse",
  f_vsn: "1.0.0"
  services: [                              // All the stuff that configures FCL
  // Authentication Service - REQUIRED
    {
      f_type: "Service",                   // Its a service!
      f_vsn: "1.0.0",                      // Follows the v1.0.0 spec for the service
      type: "authn",                       // the type of service it is
      ...
    },
  // Authentication Refresh Service
  {
    f_type: "Service",                      // Its a service!
    f_vsn: "1.0.0",                         // Follows the v1.0.0 spec for the service
    type: "authn-refresh",                  // The type of service it is
    method: "HTTP/POST",                    // Back Channel
    endpoint: "authentication-refresh-url", // The authentication refresh endpoint
    uid: "awesome-wallet#authn-refresh",    // A unique identifier for the service
    id: "0xUSER",                           // the wallets internal id for the user or flow address
    data: {
      f_type: "authn-refresh",
      f_vsn: "1.0.0",
      address: "0xUSER",                    // The user's address with prefix
    },                                      // will be included in the requests body
    params: { }                             // will be included in the requests url
  },
  /// Additional Services
  ...
  ]
})

```

- 2021-12-07 -- Internal: Updates `fcl.verifyUserSignatures` adding additional validation. Extends Cadence query script to add key weight threshold and enforces signatures need to be from a single account address.

## 0.0.78-alpha.9 - 2021-12-03

- 2021-12-01 -- Internal: Wrap authz in resolve to dedupe accounts. Remove user `notExpired` check.
- 2021-11-30 -- Allow apps to add opt-in wallets in Discovery with config.

```javascript
import {config} from "@onflow/fcl"

// Include supports discovery.wallet or discovery.authn.endpoint
config({
  "discovery.wallet": "https://fcl-discovery.onflow.org/testnet/authn",
  "discovery.authn.endpoint":
    "https://fcl-discovery.onflow.org/api/testnet/authn",
  "discovery.authn.include": ["0x123"], // Service account address
})
```

- 2021-11-22 -- Updates to use account key `HashAlgorithm` in `verifyUSerSignatures` instead of default `SHA3_256`
- 2021-11-19 -- Update `buildUser` with with correct data prop for `expiresAt`

## 0.0.78-alpha.8 - 2021-11-17

- 2021-11-17 -- VSN `@onflow/types` 0.0.5 -> 0.0.6

## 0.0.78-alpha.7 - 2021-11-12

- 2021-11-10 -- Allow configurable Discovery in FCL via `fcl.discovery` with current support for authn services.

An app developer will now be able to list services for authentication ("authn") in their application and authenticate without having to point directly at a single wallet or `"discovery.wallet"` which is a pre-constructed UI for Flow services.

To use this, first set in the config the API endpoint for fetching Flow services.

```javascript
import {config} from "@onflow/fcl"

config({
  "discovery.authn.endpoint":
    "https://fcl-discovery.onflow.org/api/testnet/authn",
})

// On mainnet, you can use https://fcl-discovery.onflow.org/api/authn
```

Then in an application you can get services with the following:

```javascript
import * as fcl from "@onflow/fcl"

fcl.discovery.authn.subscribe(callback)

// OR

fcl.discovery.authn.snapshot()
```

This will return a list a services which you can then authenticate with the following:

```javascript
fcl.authenticate({service})
```

An example React component could then wind up looking like this:

```javascript
import "./config"
import {useState, useEffect} from "react"
import * as fcl from "@onflow/fcl"

function Component() {
  const [services, setServices] = useState([])
  useEffect(
    () => fcl.discovery.authn.subscribe(res => setServices(res.results)),
    []
  )

  return (
    <div>
      {services.map(service => (
        <button
          key={service.provider.address}
          onClick={() => fcl.authenticate({service})}
        >
          Login with {service.provider.name}
        </button>
      ))}
    </div>
  )
}
```

- 2021-11-10 -- Updates pop/tab to reject on force window close
- 2021-10-29 -- Removes default `config.fcl.appDomainTag` and updates docs.

## 0.0.78-alpha.6 - 2021-10-25

- 2021-10-25 -- Corrects import error in `fcl.events`

## 0.0.78-alpha.5 - 2021-10-21

- 2021-10-21 -- SDK VSN 0.0.55 -> 0.0.56-alpha.1

## 0.0.78-alpha.4 - 2021-10-19

- 2021-10-12 -- Adds `execExtRPC` strategy for use with browser extension communication. Adds new `WalletUtils.ready`, `WalletUtils.redirect` and `WalletUtils.injectExtService` utils. `WalletUtils.ready` takes a callback and optional message data. It tells FCL that the frame/window/tab is ready to receive messages and calls your callback with `FCL:VIEW:READY:RESPONSE` data.

Internal: Extracts message event listener callbacks into `buildMessageHandler` on `extension`, `pop`, `frame`, and `tab`

An extension can be made available in FCL Discovery and displayed as a wallet choice by pushing a `Service` of type `authn` to an array of `fcl_extensions` on the window object or using the `injectExtService` wallet utility.

```javascript
let AuthnService = {
  f_type: "Service",
  f_vsn: "1.0.0",
  type: "authn",
  uid: "awesome-wallet-extension#authn",
  endpoint: "awesome-wallet-extension",
  method: "EXT/RPC",
  id: "0x5678",
  identity: {
    address: "0x1234",
  },
  provider: {
    address: null,
    name: "Awesome Wallet Extension",
    icon: null,
    description: "Awesome Wallet Extension for Chrome",
  },
}

if (!Array.isArray(window.fcl_extensions)) {
  window.fcl_extensions = []
}
window.fcl_extensions.push(AuthnService)
```

```javascript
import {WalletUtils} from "@onflow/fcl"

WalletUtils.injectExtService(AuthnService)
```

- 2021-10-01 -- Simplify passing `currentUser` data into args with the ability to pass `currentUser` as param instead of a creating a user snapshot before.

Examples of `currentUser` as a param.

```javascript
import {query, currentUser} from "@onflow/fcl"

await query({
  cadence: `
    pub fun main(currentUser: Address): Address {
      return currentUser
    }
  `,
  args: (arg, t) => [currentUser],
})
```

## 0.0.78-alpha.3 - 2021-10-01

- 2021-10-01 -- SDK VSN 0.0.55
- 2021-09-29 -- Update `pop` height
- 2021-09-27 -- Add warning for using currentUser server side and turn off local and session storage if server side

## 0.0.78-alpha.2 - 2021-09-23

- 2021-09-22 -- Adds `service` and `config` to `http-post` data
- 2021-09-21 -- Updates `fcl.authenticate` to include msg data `timestamp`, and optional `appDomainTag` for signing (**Proveable Authn**)
- 2021-09-20 -- Adds wallet utilities for encoding provable authentication messages

## 0.0.78-alpha.1 - 2021-09-17

- 2021-09-16 -- Adds redirect option for `POP/RPC` and `TAB/RPC`

## 0.0.77 - 2021-09-17

- 2021-09-16 -- Simplifies current user syntax from `currentUser()` to `currentUser`

Examples of `currentUser` functionality.

```javascript
import {currentUser} from "@onflow/fcl"

currentUser.snapshot()
currentUser.subscribe(callback)
```

- 2021-09-14 -- Adds `WalletUtils.CompositeSignature` constructor.
- 2021-08-27 -- Adds `config.fcl.storage` allowing for injection of desired storage option. Defaults to `SESSION_STORAGE`.

```javascript
export const SESSION_STORAGE = {
  can: true,
  get: async key => JSON.parse(sessionStorage.getItem(key)),
  put: async (key, value) => sessionStorage.setItem(key, JSON.stringify(value)),
}
```

## 0.0.77-alpha.4 - 2021-08-27

- 2021-08-27 -- Add `WalletUtils.encodeMessageFromSignable`.
- 2021-08-25 -- Move `verifyUserSignatures` to separate module, removing from `currentUser`
- 2021-08-20 -- Remove default `config`, fix message check in `onMessageFromFCL`
- 2021-08-19 -- Add `WalletUtils.approve`, `WalletUtils.decline`, and `WalletUtils.close`
- 2021-08-12 -- Update `pop`, `tab` onReady response to include deprecated `FCL:FRAME:READY:RESPONSE`.
- 2021-08-10 -- Update `frame` onReady response to include deprecated `FCL:FRAME:READY:RESPONSE`.

## 0.0.77-alpha.3 - 2021-08-04

- 2021-08-04 -- Adds `execTabRPC` strategy.
- 2021-08-02 -- Adds `WalletUtils.onMessageFromFCL`. Update response types to use `VIEW` and add deprecation warnings for `FRAME`.

## 0.0.77-alpha.2 - 2021-07-30

- 2021-07-29 -- Adds `execLocal` to handle rendering of `http-post` service `local-view`.
- 2021-07-26 -- Updates `currentUser.authenticate()` to use execService with parameter destructuring.

## 0.0.77-alpha.1 - 2021-07-23

- 2021-07-23 -- VSN `@onflow/sdk` 0.0.53 -> 0.0.54
- 2021-07-23 -- Reverts to iFrame as default wallet method/view.

## 0.0.77-pain.1

> **PAIN** builds are experimental builds that change a fundamental feature in a not yet backwards compatible way.
> They exist to test and vet ideas and concepts that may make their way into a non-pain build.
> They are called a **PAIN** build because if you are not the intended consumer of the build you will have a really bad time.
> Please use non-pain builds to avoid pain.

- 2021-07-21 -- **EXPERIMENTAL** **Replaces iFrame with pop-up window for authn and authz**. Add `WalletUtils` and use POP/RPC as default.

## 0.0.[75-76] - 2021-07-201

- 2021-07-21 -- SDK VSN 0.0.52
- 2021-07-21 -- Updates `verifyUserSignatures` to use `account` util from `@onflow/sdk`

## 0.0.74 - 2021-07-20

- 2021-07-20 -- SDK VSN 0.0.51

## 0.0.74-alpha.2 - 2021-07-19

- 2021-07-15 -- Update `verifyUserSignature` to pass publicKey signingAlgos into Cadence script. Allows for successfull verification of supported algorithms ECDSA_P256 and ECDSA_secp256k1
- 2021-07-14 -- Updates `fcl.serialize` to use new `config.first`

## 0.0.74-alpha.1 - 2021-07-13

- 2021-07-13 -- VSN `@onflow/sdk` 0.0.50 -> 0.0.51-alpha.1
  - Includes a fix for an issue in what `fcl.serialize` returned.
  - Exposed new `TestUtils` Top Level
  - Includes some new `config` functionality.
    - `config().put("foo", "bar")` -> `config.put("foo", "bar")` config no longer needs to be invoked to access actor methods.
    - `config.overload` allows for injecting configuration data during the execution of the callback.
    - `config.first(["foo", "bar"], "fallback")` will return the first non null config or the fallback.
    - `config.all()` will return the current configuration information.
- 2021-07-08 -- Adds `verifyUserSignatures` util to `currentUser()` and refines use of `composite-signature` normalization
- 2021-06-30 -- Updates `fcl.serialize` to fix setting `resolveFunction`

Examples of `config` functionality.

```javascript
import {config} from "@onflow/fcl"

expect(await config.all()).toEqual({})

config({
  "foo.bar": "baz",
})
config.put("bob", "pat")

expect(await config.all()).toEqual({
  "foo.bar": "baz",
  bob: "pat",
})

var ret = await config.overload({bob: "bill"}, async () => {
  expect(await config.all()).toEqual({
    "foo.bar": "baz",
    bob: "bill",
  })
  return "woot"
})

expect(ret).toBe("woot")

expect(await config.all()).toEqual({
  "foo.bar": "baz",
  bob: "pat",
})

expect(await config.first(["bax", "foo.bar"], "FALLBACK")).toBe("baz")
expect(await config.first(["nope", "oh-no"], "FALLBACK")).toBe("FALLBACK")
```

## 0.0.73 - 2021-06-21

- 2021-06-21 -- Full VSN release of FCL.

## 0.0.73-alpha.3 - 2021-06-18

- 2021-06-18 -- Removed `temp.js`

## 0.0.73-alpha.2 - 2021-06-17

- 2021-06-17 -- VSN `@onflow/sdk` 0.0.48 -> 0.0.50
- 2021-06-17 -- Used `config` from `@onflow/sdk` instead of `@onflow/config`

## 0.0.73-alpha.1 - 2021-06-17

- 2021-06-17 -- **EXPERIMENTAL** Exposes new top level `fcl.mutate` function (@orodio)

New **EXPERIMENTAL** `fcl.mutate` functionality (mirors `fcl.query` but for transactions) can be used like this.

```javascript
// profile contract on testnet
import * as fcl from "@onflow/fcl"

// address overloading works for fcl.mutate too
fcl.config().put("0xProfile", "0xba1132bc08f82fe2")

// defaults to current user for all signatory roles
await fcl.mutate({
  cadence: `
    import Profile from 0xProfile

    transaction(name: String) {
      prepare(acct: AuthAccount) {
        acct
          .borrow<&{Profile.Owner}>(from: Profile.privatePath)
          .setName(name)
      }
    }
  `,

  args: (arg, t) => [arg("qvvg", t.String)],

  limit: 65,
})

// you can use a custom authorization function for all three signatory roles
import {myAuthzFn} from "./my-authz-fn"

const INIT_PROFILE_CONTRACT = `
  import Profile from 0xProfile

  transaction {
    prepare(acct: AuthAccount) {
      if !Profile.check(acct.address) {
        acct.save(<- Profile.new(), to: Profile.privatePath)
        acct.link<&Profile.Base{Profile.Public}>(Profile.publicPath, target: Profile.privatePath)
      }
    }
  }
`

await fcl.mutate({
  cadence: INIT_PROFILE_CONTRACT,
  authz: myAuthzFn,
})

// individual roles can be overloaded
// mutate will prefer specific roles over authz over current user
// the following is the same as passing myAuthzFn in as authz

await fcl.mutate({
  cadence: INIT_PROFILE_CONTRACT,
  proposer: myAuthzFn,
  payer: myAuthzFn,
  authorizations: [myAuthzFn],
})

// the following would use myAuthzFn to pay for the transaction but the current user for everything else

await fcl.mutate({
  cadence: INIT_PROFILE_CONTRACT,
  payer: myAuthzFn,
})

// the following would use myAuthzFn for the payer and the second authorization but current user for everything else

await fcl.mutate({
  cadence: `
    transaction {
      prepare(currentUser: AuthAccount, myAuthzFn: AuthAccount) {
        // some transaction that requires two authorization accounts
      }
    }
  `,
  payer: myAuthzFn,
  authorizations: [fcl.authz, myAuthzFn],
})

// the following would use myAuthzFn for everything, but the current user will be the authorizer

await fcl.mutate({
  cadence: INIT_PROFILE_CONTRACT,
  authz: myAuthzFn,
  authorizations: [fcl.authz],
})
```

## 0.0.72 - 2021-06-16

- 2021-06-16 -- Full VSN Release `@onflow/fcl` 0.0.71 -> 0.0.72
- 2021-06-16 -- VSN `@onflow/sdk` 0.0.47 -> 0.0.48
- 2021-06-16 -- Exports `encodeMessageFromSignable` from `@onflow/sdk`

## 0.0.71 - 2021-06-04

- 2021-06-04 -- Full VSN Release `@onflow/fcl` 0.0.71-alpha.1 -> 0.0.71

## 0.0.71-alpha.1 - 2021-06-03

- 2021-06-03 -- Adds `hid *` permission policy to iframe rendered in render-frame strategy.
- 2021-05-28 -- Adds `fcl.currentUser().signUserMessage` and `user-signature` service normalizer. `fcl.currentUser().signUserMessage` allows for sending of unencrypted message data to a connected wallet provider or service to be signed with user's private key.
- 2021-05-27 -- Updates `fcl.serialize` to return serialized voucher
- 2021-05-27 -- VSN `@onflow/sdk` 0.0.46-alpha.1 -> 0.0.47-alpha.1

## 0.0.70 - 2021-05-10

- 2021-04-27 -- Full VSN Release `@onflow/fcl` 0.0.70-alpha.1 -> 0.0.70

## 0.0.70-alpha.1 - 2021-05-05

- 2021-05-05 **BREAKING** -- VSN `@onflow/sdk` 0.0.45 -> 0.0.46-alpha.1 -- Prepends a transaction domain tag to encoded payload and envelope messages. Transaction domain tags allow signers to identify which messages are intended to represent encoded transactions, and which are not. The Flow protocol has been updated (as of May 5th 2021) to both accept signatures produced from messages prepended with a transaction domain tag, and from messages that are not. The next spork (time and date of next spork are TBD) will strictly require all signatures for transnactions to have been produced from messages prepended with a transaction domain tag. This breaking change requires all users of Flow Client Library and the Flow JavaScript SDK to update their versions to a version greater than or equal to the verison that this change was included in.

## 0.0.69 - 2021-04-28

- 2021-04-28 -- Exposes `invariant` and `validator` builders from FCL

## 0.0.68 - 2021-04-27

- 2021-04-27 -- Full VSN Release `@onflow/fcl` 0.0.68-alpha.22 -> 0.0.68

## 0.0.68-alpha.22 - 2021-04-26

- 2021-04-26 -- Fixed an issue where `config()` was being called as `fcl.config()`, but `fcl` wasn't available.

## 0.0.68-alpha.21 - 2021-04-21

- 2021-04-21 -- **BREAKING** The experimental feature `fcl.meta` which allowed for a transaction to send along meta data to an authorization function has been removed because of the unprovable nature of its data and our strict trustless requirements. We believe this removal is in the best interest for js-sdk/fcl end users and will be looking into alternative approaches that provide the same functionality but in a more provable/trustless way. We have no ETA on this features replacement.

## 0.0.68-alpha.20 - 2021-04-16

- 2021-04-16 - VSN `@onflow/sdk` 0.0.45-alpha.18 -> 0.0.45-alpha.19

## 0.0.68-alpha.19 - 2021-04-14

- 2021-04-14 - FCL now imports and exports `@onflow/types` as `t` (@orodio)
- 2021-04-14 - **EXPERIMENTAL** Exposes new top level `fcl.query` function. (@orodio)

New **EXPERIMENTAL** `fcl.query` functionality can be used like this.

```javascript
import * as fcl from "@onflow/fcl"

await fcl.query({
  cadence: `
    pub fun main(a: Int, b: Int, addr: Address): Int {
      log(addr)
      return a + b
    }
  `,
  args: (arg, t) => [
    arg(7, t.Int), // a: Int
    arg(6, t.Int), // b: Int
    arg("0xba1132bc08f82fe2", t.Address), // addr: Address
  ],
})
```

## 0.0.68-alpha.18 - 2021-04-09

- 2021-04-09 -- Exposes `meta` and `getCollection` builders from SDK

## 0.0.68-alpha.10 - 2021-03-05

- 2021-03-05 -- Additional Configuration
  - `app.detail.title` -- the title of the application
  - `app.detail.icon` -- url for an icon image for the application
  - `service.OpenID.scopes` -- register interest for scopes to be returned by the wallet

New configuration works like older configuration:

```javascript
import * as fcl from "@onflow/fcl"

fcl
  .config()
  .put("app.detail.title", "My Great Application")
  .put("app.detail.icon", "https://avatars.onflow.org/avatar/dapp")
  .put("service.OpenID.scopes", "email email_verified name")
```

All OpenID data returned should be considered optional. For the time being it will be visable as a service in the current user, but in the future we will provide additional ways to subscribe and access this data.

Info on what could be there is specified in [OpenID Connect Spec](https://openid.net/specs/openid-connect-basic-1_0.html) in particular under [2.4 Scope Values](https://openid.net/specs/openid-connect-basic-1_0.html#Scopes) and [2.5 Standard Claims](https://openid.net/specs/openid-connect-basic-1_0.html#StandardClaims).

Wallets are not expected to implement the open-id service, and if they do we suggest best practice is for wallets to allow the account owner to decide what information is shared (all, none, somewhere in between), for stable applications we also highly recommend you do not depend on a wallet returning this info for a given user.

As always with services, an example of the data they are supposed to return can be found in their normalization file: [normalize/open-id.js](https://github.com/onflow/flow-js-sdk/blob/master/packages/fcl/src/current-user/normalize/open-id.js)

## 0.0.68-alpha.9 - 2021-03-02

- 2020-03-02 -- VSN `@onflow/sdk` 0.0.45-alpha.9 -> 0.0.45-alpha.10

## 0.0.68-alpha.[7..8] - 2021-03-02

- 2021-03-02 -- Authn now uses frame mechanism from Authz flow.
  - This is in prep for sending config details to wallet during handshake.
  - Enables standardized `FCL:FRAME:CLOSE` message for closing frame
  - Introduces a new standardized response `FCL:FRAME:RESPONSE` for wallets to pass the final response back.
  - (EXPERIMENTAL) Introduces a new standardized open response `FCL:FRAME:OPEN` for wallets that need to escape the iframe in web browser
  - This is backwards compatible with older versions of the FCL wallet spec, but will not be included in V1 release.
- 2021-03-02 -- Introduced new config `discovery.wallet`. This will eventually replace `challenge.handshake`.
  - Current implementation will look for `discovery.wallet` first and then fallback onto `challenge.handshake`.
  - This is backwards compatible with older version of FCL, but will not be included in V1 release.

New Wallet AUTHN Flow will now look like this:

```
FCL                         Wallet
 |                            |
 |-------[Render Frame]------>|
 |                            |
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~[OPTIONAL READY FLOW]
 |<----[FCL:FRAME:READY]------|  // Enables wallet to receive
 |----[APP DETAILS/NEEDS]---->|  // Application specific details/needs
 |     [handles details]------*
 |                            |
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~[OPTIONAL TAB FLOW] (EXPERIMENTAL)
 |<----[FCL:FRAME:OPEN]-------|  // Some authentication flows cant
 |------[Open Tab]----------->|  // Be handled in an iframe
 |                            |
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~[GOLDEN AUTH PATH]
 |<----[FCL:FRAME:RESPONSE]---|
 *-------[handles auth]       |
 |-------[Close Frame]---X    |
 |--------[Close Tab]----X    |
 |                            |
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~[CANCEL AUTH PATH]
 |<----[FCL:FRAME:CLOSE]------|
 |-------[Close Frame]---X    |
 |--------[Close Tab]----X    |
 |                            |
```

`FCL:FRAME:OPEN` payload needs to look like this:

```json
{
  "type": "FCL:FRAME:OPEN",
  "endpoint": "https://google.com"
}
```

> The opened tab (web view in something like Unity/iOS?) will not be responsible for sending the Final `FCL:FRAME:RESPONSE` or `FCL:FRAME:CLOSE` messages (this will need to be the frame), but FCL will attempt to close the tab one authn is complete.

## 0.0.68-alpha.[1..6] - 2021-02-02

- 2021-02-02 -- Adds support for new `GetEvents`, `GetBlockHeader`, `GetBlock`, `GetTransaction` interactions.

## 0.0.67 - 2021-01-14

- 2021-01-14 -- General Cleanup

## 0.0.67-alpha.43 - 2020-12-11

- 2020-12-11 -- Disables setting keyId in fetch signatures

## 0.0.67-alpha.42 - 2020-12-11

- 2020-12-11 -- VSN `@onflow/sdk-send` 0.0.9 -> 0.0.10
- 2020-12-11 -- VSN `@onflow/sdk-resolve` 0.0.9 -> 0.0.10
- 2020-12-11 -- VSN `@onflow/sdk-account` 0.0.7 -> 0.0.8

## 0.0.67-alpha.41 - 2020-12-08

- 2020-12-08 -- FIX: issue with multi-sig from same account (@boczeraturl)

## 0.0.67-alpha.[36..40] - 2020-12-03

- 2020-12-03 -- Updates `@onflow/send` -- This includes some fixes involving how signatures are added to transaction rpc calls
- 2020-12-03 -- FIX: `fcl.serialize` respects injected resolver propoerly.
- 2020-12-03 -- Prep for Resolver extraction.

## 0.0.67-alpha.[19..35] - 2020-11-26

- 2020-11-26 -- `pre-authz` golden path tested on testnet with dapper
- 2020-11-26 -- Early exit `HTTP/POST` so that it can work like an `HTTP/RPC`
- 2020-11-26 -- added an alias from `HTTP/RPC` to `HTTP/POST`
- 2020-11-26 -- additional `pre-authz` debuging in current user
- 2020-11-26 -- service-endpoint includes `l6n` as `window.location.origin`

## 0.0.67-alpha.18 - 2020-11-26

- 2020-11-26 -- `pre-authz` flatmaps exec-services response

## 0.0.67-alpha.17 - 2020-11-23

- 2020-11-23 -- `pre-authz` flow executed
- 2020-11-23 -- `fcl.serialize` now accepts custom resolver

## 0.0.67-alpha.16 - 2020-11-20

- 2020-11-20 -- Rough in pre-authz flow

## 0.0.67-alpha.[7..15] - 2020-11-19

- 2020-11-19 -- Experimental IFRAME/RPC strategy

## 0.0.67-alpha.6 - 2020-11-19

- 2020-11-19 -- IFRAMES can be closed with an `{type: "FCL:FRAME:CLOSE"}` postMessage

## 0.0.67-alpha.5 - 2020-11-19

- 2020-11-19 -- Auto replace known cadence addresses
- 2020-11-17 -- Auto resolve sequence number for proposer if its not there

> Example references this contract: [Testnet Profile Contract](https://flow-view-source.com/testnet/account/0x1d007d755706c469)

```javascript
// Config fcl with contract addresses
// Their config key must start with `0x`
fcl.config()
  .put("env", "testnet")
  .put("0xProfile", "0x1d007d755506c469")

// Your transactions can now look like this
await fcl.send([
  fcl.proposer(fcl.authz),
  fcl.authorizations([fcl.authz])
  fcl.payer(fcl.authz),
  fcl.limit(35),
  fcl.args([
    fcl.arg("Bob", t.String),
  ]),
  fcl.transaction`
    import Profile from 0xProfile

    transaction(displayName: String) {
      prepare(account: AuthAccount) {
        account
          .borrow<&{Profile.Owner}>(from: Profile.privatePath)!
          .setDisplayName(displayName)
      }
    }
  `
])

// Your scripts can now look like this
await fcl.send([
  fcl.args([
    fcl.arg("0xba1132bc08f82fe2", t.Address),
  ]),
  fcl.script`
    import Profile from 0xProfile

    pub fun main(address: Address): Profile.ReadOnly? {
      return Profile.fetchProfile(address)
    }
  `,
]).then(fcl.decode)
```

## 0.0.67-alpha.4 - 2020-11-17

- 2020-11-17 -- Fix small issues involving NPM

## 0.0.67-alpha.3 - 2020-11-17

- 2020-11-17 -- Fix issue where validation for composite signature for keyId of zero was counted as false

## 0.0.67-alpha.2 - 2020-11-17

- 2020-11-17 -- Injected in a custom resolver in prep for pre-authz service

## 0.0.67-alpha.1 - 2020-11-17

- 2020-11-17 -- Added `fcl.account`
- 2020-11-17 -- Added `fcl.reauthenticate`
- 2020-11-17 -- Added `fcl.authz`
- 2020-11-17 -- Added `fcl.signUp`
- 2020-11-17 -- Added `fcl.logIn`

```javascript
// fcl.account - A convenience function for fetching an account
/* old */ await fcl.send([fcl.getAccount("0x1d007d755706c469")])
/* new */ await fcl.account("0x1d007d755706c469")

// fcl.reauthenticate - Logs the current user out before attempting to authenticate again
await fcl.reauthenticate()

// fcl.authz - alias for fcl.currentUser().authorization
await fcl.send([
  fcl.transaction(txCode),
  fcl.proposer(fcl.authz),
  fcl.payer(fcl.authz),
  fcl.authorizations([fcl.authz]),
])

// fcl.signUp and fcl.logIn - Currently these alias fcl.authenticate, eventually they will pass additional context to the wallets
await fcl.signUp()
await fcl.logIn()
```

## 0.0.67-alpha.0 - 2020-11-17

- 2020-11-17 -- VSN @onflow/sdk-latest-block 0.0.2 -> 0.0.3
- 2020-11-17 -- VSN @onflow/sdk-account 0.0.2 -> 0.0.3
- 2020-11-17 -- VSN @onflow/sdk-send 0.0.3 -> 0.0.5

## 0.0.66 - 2020-11-09

- 2020-11-09 -- Adds a handshake mechanism to exec-authz-service to allow clients to request signable message when ready

## 0.0.65 - 2020-11-04

- 2020-11-05 -- sansPrefix required addresses

## 0.0.64 - 2020-11-04

- 2020-11-04 -- VSN `@onflow/sdk-send` 0.0.2 -> 0.0.3

## 0.0.63 - 2020-11-04

- 2020-11-04 -- VSN `@onflow/sdk-resolve` 0.0.3 -> 0.0.4

## 0.0.62 - 2020-10-29

- 2020-10-29 -- Fixed an issue regarding fetching transaction statuses

## 0.0.61 - 2020-10-28

- 2020-10-28 -- VSN `@onflow/sdk-decode` 0.0.0 -> 0.0.1
- 2020-10-28 -- VSN `@onflow/sdk-build-authorizations` 0.0.0 -> 0.0.1
- 2020-10-28 -- VSN `@onflow/sdk-latest-block` 0.0.0 -> 0.0.2
- 2020-10-28 -- VSN `@onflow/sdk-account` 0.0.0 -> 0.0.2
- 2020-10-28 -- VSN `@onflow/sdk-send` 0.0.0 -> 0.0.2
- 2020-10-28 -- VSN `@onflow/sdk-resolve` 0.0.0 -> 0.0.3
- 2020-10-28 -- Added in `persistSession` config flag (defaults to true)

## 0.0.61-alpha.5 - 2020-10-08

- 2020-10-08 -- ADD PROXY `latestBlock` exports directly to `@onflow/sdk-latest-block`
- 2020-10-08 -- ADD PROXY `account` exports directly to `@onflow/sdk-account`
- 2020-10-08 -- PROXY `send` export directly to `@onflow/sdk-send`
- 2020-10-08 -- PROXY `decode` export directly to `@onflow/sdk-decode`
- 2020-10-08 -- VSN `@onflow/decode` 0.0.7 -> 0.0.8
- 2020-10-08 -- VSN `@onflow/sdk-build-get-account` 0.0.0 -> 0.0.1

## 0.0.61-alpha.4 - 2020-10-07

- 2020-10-07 -- remove resolvers and replace with @onflow/sdk-resolve

## 0.0.61-alpha.3 - 2020-10-07

- 2020-10-07 -- remove use of sdk
- 2020-10-07 -- use `@onflow/sdk-resolve-ref-block-id` package directly

## 0.0.61-alpha.2 - 2020-10-07

- 2020-10-07 -- VSN `@onflow/sdk-resolve-signatures` 0.0.0 -> 0.0.1
- 2020-10-07 -- VSN `@onflow/sdk` 0.0.35 -> 0.0.37

## 0.0.61-alpha.1 - 2020-10-07

- 2020-10-07 -- proxy to packages directly instead of via sdk
- 2020-10-07 -- allow for authn iframe to be closed by content

## 0.0.60 -- 2020-10-05

- 2020-10-05 -- iframe feature policy allows usb on all contexts

## 0.0.59 -- 2020-09-29

- 2020-09-29 -- VSN `@onflow/sdk` 0.0.31 -> 0.0.35
- 2020-09-29 -- use `@onflow/sdk-resolve-validators` package directly
- 2020-09-29 -- use `@onflow/sdk-resolve-signatures` package directly
- 2020-09-29 -- use `@onflow/sdk-resolve-accounts` package directly

## 0.0.58 -- 2020-09-29

- 2020-09-29 -- use `@onflow/sdk-resolve-arguments` package directly

## 0.0.57 -- 2020-09-29

- 2020-09-29 -- add a z-index value to service frame
- 2020-09-29 -- resolveParams -> resolveCadence

## 0.0.56 -- 2020-09-04

- 2020-09-04 -- Fixed some issues involving FCL talking to iframes

## 0.0.55 -- 2020-09-04

- 2020-09-04 -- Wallet Provider Services

## 0.0.54 -- 2020-09-03

- 2020-09-03 -- Authn and Authz iframes allow usb feature policy.

## 0.0.53 -- 2020-08-26

- 2020-08-26 -- `@onflow/sdk` VSN `0.0.30` -> `0.0.31`
- 2020-08-20 -- Adds Authorization Function Documentation

## 0.0.52 -- 2020-08-18

- 2020-08-18 -- Authorization persists using local storage (this isnt the final solution, but creates the experience we want for now)
- 2020-08-18 -- authn and authz iframes are now full width and height
- 2020-08-18 -- Fixed an issue where unauthenticating the currentUser wasnt clearning the address on the user
- 2020-08-18 -- Added two new util functions `fcl.withPrefix` and `fcl.sansPrefix`

## 0.0.51 -- 2020-08-14

- 2020-08-14 -- `fcl.tx(resp).once*` throw errors when statusCode is non-zero, errors can be suppressed (previous behaviour) py passing `{suppress: true}` to the `.once*` function.
- 2020-08-14 -- `fcl.tx(resp).subscribe(callback)` callback will only be called when the status is different to what it was before.

## 0.0.50 -- 2020-08-14

- 2020-08-14 -- Fixeds some small bugs due to typos

## 0.0.49 -- 2020-08-13

- 2020-08-13 -- Adds export of `fcl.events(eventKey).subscribe(callback)`

## 0.0.48 -- 2020-08-13

- 2020-08-13 -- Removed `resolveSequenceNumber` from default resolver

## 0.0.47 -- 2020-08-13

- 2020-08-13 -- `@onflow/config` VSN `0.0.1` -> `0.0.2`
- 2020-08-13 -- `@onflow/util-actor` VSN `0.0.1` -> `0.0.2`
- 2020-08-07 -- Adds resolvers `resolveRefBlockId` and `resolveProposerSequenceNumber` to fcl resolve.

## 0.0.46 -- 2020-07-27

- 2020-07-27 -- VSN `@onflow/sdk` 0.0.28 -> 0.0.30

## 0.0.45 -- 2020-07-22

- 2020-07-22 -- deprecate fields on CurrentUser data structure

## 0.0.44 -- 2020-07-22

- 2020-07-22 -- added `tx(r).onceFinalized()` and `tx(r).onceExecuted`.
- 2020-07-22 -- added `tx.isFinalized(txStatus)` and `tx.isExecuted(txStatus)`.

## 0.0.43 -- 2020-07-21

- 2020-07-21 -- VSN `@onflow/sdk` 0.0.27 -> 0.0.28

## 0.0.42 -- 2020-07-20

- 2020-07-20 -- Convert config and actor to use external `@onflow/confg` and `@onflow/util-actor`

## 0.0.41 -- 2020-07-13

- 2020-07-13 -- VSN `@onflow/sdk` 0.0.26 -> 0.0.27

## 0.0.40 -- 2020-07-09

- 2020-07-09 -- Bring in resolveSignatures fix from SDK that passes arguments to the encoder function

## 0.0.39 -- 2020-07-09

- 2020-07-09 -- Expose `fcl.args` and `fcl.arg`
- 2020-07-09 -- Bring in resolveSignatures fix from SDK that deals with signatures better
- 2020-07-08 -- Updates FCL's resolve implementation according to the new resolvers available in the SDK.
- 2020-07-08 -- VSN `@onflow/sdk` 0.0.23 -> 0.0.24

## 0.0.38

- 2020-06-23 -- Added `fcl.serialize`

## 0.0.37 -- 2020-06-04

- 2020-06-04 -- VSN `@onflow/sdk` 0.0.22 -> 0.0.23

## 0.0.36 -- 2020-06-03

- 2020-06-03 -- VSN `@onflow/sdk` 0.0.21 -> 0.0.22

## 0.0.35 -- 2020-05-15

- Rerelease because funky build

## 0.0.34 -- 2020-05-15

- 2020-05-15 -- VSN `@onflow/sdk` 0.0.20 -> 0.0.21

## 0.0.33 -- 2020-05-07

- 2020-05-07 -- VSN `@onflow/sdk` 0.0.19 -> 0.0.20

## 0.0.32 -- 2020-05-07

- 2020-05-07 -- add .npmignore with src/

## 0.0.31 -- 2020-05-07

- 2020-05-07 -- VSN `@onflow/sdk` 0.0.17 -> 0.0.19

## 0.0.30 -- 2020-05-07

- 2020-05-07 -- Created and exposed a top level `tx` function
- 2020-05-07 -- actor spawn now excepts an object of handlers
- 2020-05-07 -- Moved common actor logic into actor (where, all)
- 2020-05-07 -- Exposed fcls actor registry to window

## 0.0.29 -- 2020-05-06

- 2020-05-06 -- Fixed an issue with custom decoders
- 2020-05-06 -- Leverage named imports to enable better tree shaking
- 2020-05-06 -- Proxy `@onflow/sdk` through fcl to enable single import
- 2020-05-06 -- VSN `@onflow/sdk` 0.0.16 -> 0.0.17
- 2020-05-05 -- VSN `@onflow/sdk` 0.0.15 -> 0.0.16
- 2020-05-05 -- Update resolvers based on sdk update
- 2020-05-05 -- VSN `@onflow/sdk` 0.0.14 -> 0.0.15 Breaking Change
- 2020-04-29 -- authn challenge response no longer locks to handshake origin
- 2020-04-28 -- `fcl.user(addr).info()` does a `send([getAccount(addr)])` returning the `account`
- 2020-04-23 -- `fcl.authenticate()` renders iframe for config: `challenge.handshake`

## 0.0.28 -- 2020-04-20

- 2020-04-20 -- VSN `@onflow/sdk` 0.0.12 -> 0.0.14
- 2020-04-20 -- Remove `@onflow/send` in favour of `@onflow/sdk`

## 0.0.27 -- 2020-04-18

- 2020-04-18 -- VSN sdk 0.0.4 -> 0.0.5
- 2020-04-18 -- VSN sdk 0.0.11 -> 0.0.12
- 2020-04-18 -- VSN jest 25.1.0 -> 25.3.0
- 2020-04-18 -- VSN microbundle 0.11.0 -> 0.12.0-next.8

## 0.0.26 -- 2020-04-17

- Pre Changelog
