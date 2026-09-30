# Appwrite setup

1. Create an Appwrite project and add `localhost` plus your production domain as Web platforms.
2. Create a server API key with database, collection, attribute, and index write scopes.
3. Copy `.env.example` to `.env.local` and fill in the endpoint, project ID, database ID, and API key.
4. Run `pnpm setup:appwrite` once. The script creates the database, `posts`, `comments`, `profiles`, and `cards` collections, their fields, indexes, and the `card-images` bucket.
5. Restart `pnpm dev` after changing environment variables.

The API key is server-only and is never imported into the browser bundle. Card documents are publicly readable for shared binders, while writes are restricted to the authenticated owner. The card image bucket allows public reads so shared binder images render.

## MyStuffsBetter database structure

Database ID: `threadline`

### `cards`

| Attribute | Type | Required | Purpose |
|---|---|---:|---|
| `ownerId` | string, 64 | yes | Appwrite user ID that owns the card |
| `name` | string, 180 | yes | Card name |
| `setName` | string, 180 | yes | Card set or expansion |
| `rarity` | string, 64 | yes | Rarity label |
| `condition` | string, 64 | yes | Physical condition |
| `imageId` | string, 64 | no | File ID in the `card-images` bucket |
| `notes` | string, 1000 | no | Collector notes |
| `createdAt` | datetime | yes | Creation timestamp |

Indexes:

- `cards_owner_created`: `ownerId` ascending, `createdAt` descending

### `profiles`

Stores public collector profile data: `userId` (string 64), `displayName` (string 128), `bio` (string 5000, optional), and `createdAt` (datetime).

### `posts`

Stores community posts: `title` (string 180), `content` (string 10000), `authorId` (string 64), `authorName` (string 64), `topic` (string 64, optional), `imageId` (string 64, optional), `voteCount` (integer), and `createdAt` (datetime).

### `comments`

Stores post comments: `postId` (string 36), `content` (string 5000), `authorId` (string 64), `authorName` (string 64), and `createdAt` (datetime).

Indexes:

- `posts_created_at`: `createdAt` descending
- `comments_post_created`: `postId` ascending, `createdAt` ascending

### Storage

- `card-images`: public read, per-file security, 8 MB maximum, image extensions only.
- `post-images`: public read, per-file security, 8 MB maximum, image extensions only.
