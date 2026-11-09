<!-- Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved. -->
<!-- SPDX-License-Identifier: Apache-2.0 -->

# Savvy Navvy subscription transport fix

This fork branch is based on the official `amplify_api_dart-v0.5.23` release
(commit `00dc88f3df9b6137b57532fc0b1c7ba6dbe0dae6`). Nassau pins a commit from
this branch as a Git dependency using `packages/api/amplify_api_dart`.

The only runtime change is in `lib/src/graphql/web_socket/blocs/web_socket_bloc.dart`.
Incoming data, errors, start acknowledgements, and duplicate completion messages
for subscriptions that have already completed are discarded because there is no
receiver. Completion cleanup is idempotent when a connection failure has already
removed the subscription. Data and GraphQL errors for active subscriptions keep
their original handling. This prevents the missing-subscription assertion and
release-mode null dereference without bypassing application error handlers.

Regression tests cover all four stale message types, continued delivery of data
and GraphQL errors to an active subscription, and clean shutdown. From this
package directory, run:

```sh
dart pub get
dart test test/web_socket/completed_subscription_test.dart
```

Nassau also retains its consumer regression tests in
`packages/captain/test/dependencies/amplify_web_socket_bloc_test.dart`.
Return Nassau to a published upstream release once it fixes this lifecycle race
and passes those tests.
