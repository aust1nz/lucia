---
_order: 0
title: "Mongoose (MongoDB)"
---

An adapter for Mongoose (MongoDB).

```ts
const adapter: (mongoose: Mongoose) => AdapterFunction<Adapter>;
```

If [`generateUserId()`](/reference/configure/lucia-configurations#generatecustomuserid) is not configured, the adapter will generate a new `ObjectId` and use the stringified version (24-character hexadecimal string) as the user id.

This adapter will not handle database connection and you will need to connect to the database manually.

### Parameter

| name     | type       | description     | optional |
| -------- | ---------- | --------------- | -------- |
| mongoose | `Mongoose` | Mongoose client |          |

### Errors

The adapter and Lucia will not not handle [unknown errors](/learn/basics/error-handling#known-errors), database errors Lucia doesn't expect the adapter to catch. When it encounters such errors, it will throw a `MongooseError`.

## Installation

```bash
npm i @lucia-auth/adapter-mongoose
pnpm add @lucia-auth/adapter-mongoose
yarn add @lucia-auth/adapter-mongoose
```

## Usage

```ts
import adapter from "@lucia-auth/adapter-mongoose";
import mongoose from "mongoose";

// set model here

const auth = lucia({
	// ,,,
	adapter: adapter(mongoose)
});
```

You'll need to handle the database connection as well.

```ts
// db.ts
import mongoose from "mongoose";

mongoose.connect(mongoUri, options);
```

## Models

### `user`

You may add additional fields to store user attributes. Refer to [Store user attributes](/learn/basics/store-user-attributes).

```ts
const User = mongoose.model(
	"user",
	new mongoose.Schema(
		{
			_id: {
				type: String
			},
			provider_id: {
				type: String,
				unique: true,
				required: true
			},
			hashed_password: String
		},
		{ _id: false }
	)
);
```

### `session`

This is not required if you're only using the Mongoose adapter for the `user` table via [`adapter.user`](/reference/configure/lucia-configurations#adapter) config.

```ts
const Session = mongoose.model(
	"session",
	new mongoose.Schema(
		{
			_id: {
				type: String
			},
			user_id: {
				type: String,
				required: true
			},
			expires: {
				type: Number,
				required: true
			},
			idle_expires: {
				type: Number,
				required: true
			}
		},
		{ _id: false }
	)
);
```