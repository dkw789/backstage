1. auth issue:


https://github.com/backstage/backstage/issues/23748



I am going to try and summarize it all in one post for others. This is for enabling Github Auth:

Create an OAuth app in Github account under Developer Settings

Github.com > Account Settings > Developer Settings > OAuth Apps
http://localhost:7007/api/auth/github/handler/frame
Add auth section to app-config.yaml:

auth:
# see https://backstage.io/docs/auth/ to learn about auth providers
environment: development
providers:
github:
development:
clientId: 6cfd...9bd
clientSecret: 7695...546c
signIn:
resolvers:
# Only one of these
- resolver: emailMatchingUserEntityProfileEmail
- resolver: emailLocalPartMatchingUserEntityName
- resolver: usernameMatchingUserEntityName
NOTE: the resolvers used is dependent on the auth provider being used!

Update the Frontend. Add the following to packages/app/src/App.tsx

import { githubAuthApiRef } from '@backstage/core-plugin-api';

const githubAuthCfg = {
id: 'github-auth-provider',
title: 'GitHub',
message: 'Sign in using GitHub',
apiRef: githubAuthApiRef,
}

...

components: {
SignInPage: props => <SignInPage {...props} auto providers={['guest', githubAuthCfg]} />,
},
Update the Backend: add github provider import in packages/backend/src/index.ts:

backend.add(import('@backstage/plugin-auth-backend-module-github-provider'))  
Make sure your Github user is defined in examples/org.yaml

# https://backstage.io/docs/features/software-catalog/descriptor-format#kind-user
apiVersion: backstage.io/v1alpha1
kind: User
metadata:
name: <github-username>
spec:
memberOf: [guests]
Hope that helps the next person that comes along. This was a bit tricky to track down for this Backstage newbie that simply wanted to do a local run to explore.

2. 