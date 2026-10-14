# app-services-rs

Reusable Rust building blocks for the backend pieces most apps end up needing: sign-in, email, configuration and secrets, file storage, and payments. Each service is a library crate you add as a git dependency and wire into your own app, not a standalone server you deploy. You configure it with a builder, and where it makes sense it comes with ready-made `axum` routes and OpenAPI docs (via `utoipa`) that you can mount as they are, or you call its service type from your own handlers instead.

Except for payments, which uses Paystack, the services are built on AWS managed services. They all share the same error and response types from `shared`, so an app that pulls in several of them handles one set of JSON shapes instead of one per service.

## Services

| Folder | Repository | What it does |
|---|---|---|
| `auth` | [festusyuma/app-auth-rs](https://github.com/festusyuma/app-auth-rs) | Sign-in through Amazon Cognito's hosted login (Authorization Code + PKCE): login and logout URLs, exchanging codes and refresh tokens, resolving the current user, plus cookie helpers and ready-made routes. |
| `email` | [festusyuma/app-email-rs](https://github.com/festusyuma/app-email-rs) | Transactional email through Amazon SES: single and concurrent batch sends, named templates for sender and subject, and HTML bodies with attachments. |
| `env` | [festusyuma/app-env-rs](https://github.com/festusyuma/app-env-rs) | Builds an app's typed config from environment variables merged with secrets from AWS Secrets Manager, with environment values taking priority. |
| `file` | [festusyuma/app-file-rs](https://github.com/festusyuma/app-file-rs) | File storage on S3, optionally behind CloudFront: presigned upload URLs, signed read URLs, and server-side upload, download and delete. |
| `payment` | [festusyuma/app-payment-rs](https://github.com/festusyuma/app-payment-rs) | Payments and payouts through Paystack: starting and verifying payments, bank lists and transfer recipients, and transfers with OTP confirmation. Records are stored in DynamoDB. |
| `shared` | [festusyuma/app-shared-rs](https://github.com/festusyuma/app-shared-rs) | The common `ServiceError`, `ServiceResult` and `ServiceResponse` types every other service returns, rendered as `{ "data": ... }` or `{ "error": ... }` JSON. |

See each repository's README for configuration and usage.

## How this repository works

Each folder is its own repository, linked here at its latest tag on `main`. Pushing a tag on a service's `main` updates its link here automatically.

## Setup

```sh
git clone --recurse-submodules git@github.com:festusyuma/app-services-rs.git
cd app-services-rs
git submodule foreach git checkout main
```

Then work inside each folder as a normal repository. The root `Cargo.toml` patches the `shared` git dependency to the local `shared/` folder, so changes to `shared` are picked up by the other services right away without tagging a release.
