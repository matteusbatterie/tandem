# API exploration

Hand-run HTTP requests against the Trello and GitHub REST APIs, used to learn the real shape of their data before designing
Tandem's canonical model (see the ADRs in [`../adr`](../adr)).

These files are documentation and a scratchpad. They are **not** automated tests and **not** part of the application.

## What's here

| File | Purpose |
| --- | --- |
| `trello.http`, `github.http` | Requests for the Visual Studio HTTP editor (VS 17.8 or later) or the VS Code REST Client extension |
| `http-client.env.json` | Non-secret defaults: base URLs, sandbox repo name |
| `http-client.env.json.user.example` | Template for your private values |
| `http-client.env.json.user` | **Your** private values for Visual Studio. Git-ignored, never committed |

## Setup

### 1. Sandbox accounts

Here is how you can setup your Trello and GitHub accounts for safe exploration. You can use your personal accounts, but it's recommended
to create fresh accounts for this purpose. Here is what you need:

- **Trello:** create a Workspace and a board named `Tandem Sandbox` with a few lists (e.g.: Open Issues; Closed Issues).
- **GitHub:** create a public repository named `tandem-sandbox` with Issues enabled.

Use fake data only. Issues in a public repository are visible to everyone.

### 2. Trello API key and token

**IMPORTANT:** Be careful with these steps as it's a sensitive operation. The API key and token grant access to your Trello account, so treat them
like a password. You can revoke them any time from your Trello account settings.

1. Go to <https://trello.com/apps/admin> and create a new app. Make sure you choose **"My app doesn't use Power-up capabilities"**
  and fill in the name, workspace, email and author.
2. Open the app and find its API key section (Trello's docs call this tab *Trello Auth*). Copy the **API key**.
3. Generate a **token** following the instructions below.

Generating a token:

- **Option A:** quick, but it never expires. In the same section, click **Generate a Token** next to your API key and
  click **Allow**. This token **does not expire** and gives broad access to your Trello account, so revoke it from your
  Trello account settings as soon as you finish exploring.
- **Option B (recommended):** scoped and expiring. Open this URL, replacing `YOUR_API_KEY`, and click **Allow**:

```text
https://trello.com/1/authorize?key=YOUR_API_KEY&scope=read,write&expiration=30days&response_type=token
```

These values should be filled in your `http-client.env.json.user` file (see below).

### 3. GitHub fine-grained token

You can skip this step entirely and use [GitHub CLI](https://docs.github.com/en/rest/quickstart) to make requests, which is the most secure approach.
**Keep in mind that the CLI will not work with the Visual Studio HTTP editor and the github.http will be rendered useless.**

If you want to generate the personal access token, [GitHub's REST API curl documentation](https://docs.github.com/en/rest/using-the-rest-api/getting-started-with-the-rest-api?tool=curl#making-a-request)
details that you need to attach a `Authorization: Bearer YOUR-TOKEN` format to your HTTP request headers.

However, the recommended way is to use a fine-grained token with the following settings:

GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token.

- **Repository access:** only select repositories → `tandem-sandbox`
- **Repository permissions:** Issues → Read and write
- **Expiration:** keep it short

For more details on the fine-grained token, see [GitHub's documentation](https://docs.github.com/en/rest/issues/issues?#create-an-issue--fine-grained-access-tokens).

These values should be filled in your `http-client.env.json.user` file (see below).

### 4. Fill in your private file

Copy `http-client.env.json.user.example` to `http-client.env.json.user` (same folder, create the file if necessary) and fill in your values:

```json
{
  "dev": {
    "trelloKey": "YOUR_TRELLO_API_KEY",
    "trelloToken": "YOUR_TRELLO_TOKEN",
    "trelloBoardId": "YOUR_TRELLO_BOARD_ID",
    "githubToken": "YOUR_GITHUB_TOKEN",
    "githubUser": "YOUR_GITHUB_USERNAME",
    "githubRepo": "GITHUB_REPO",
    "githubUserAgent": "YOUR_USER_AGENT",
    "webhookUrl": "YOUR_WEBHOOK_URL"
  }
}
```

The Trello and Github API keys were generated in the steps above. The `githubUser` is your GitHub username, and `githubRepo`
is the name of the sandbox repository you created. The `githubUserAgent` can be any string that identifies your application (for example, "Tandem API Exploration").

In the `webhookUrl`, you can use any callback URL that you control. If you don't have one, you can use a service like [Webhook.site](https://webhook.site/) to generate a temporary URL for testing.

The `.user` file is excluded by the repository's `*.user` rule. Never commit it, paste it into a chat, or include it in a
zip you share.

If you happen to commit or share your `.user` file, revoke all your tokens immediately and generate new ones.

### 5. Use VS Code (REST Client extension)

The VS Code REST Client extension does not automatically load Visual Studio's `http-client.env.json.user` file. Keep
your secrets out of the repository by adding them to your **User** `settings.json` instead (Command Palette →
**Preferences: Open User Settings (JSON)**):

```json
{
  "rest-client.environmentVariables": {
    "$shared": {
      "trelloBaseUrl": "https://api.trello.com/1",
      "githubBaseUrl": "https://api.github.com",
    },
    "dev": {
      "trelloKey": "YOUR_TRELLO_API_KEY",
      "trelloToken": "YOUR_TRELLO_TOKEN",
      "trelloBoardId": "YOUR_TRELLO_BOARD_ID",
      "webhookUrl": "YOUR_WEBHOOK_URL",
      "githubToken": "YOUR_GITHUB_TOKEN",
      "githubUser": "YOUR_GITHUB_USERNAME",
      "githubRepo": "GITHUB_REPO",
      "githubUserAgent": "YOUR_USER_AGENT"
    }
  }
}
```

Select `dev` environment by looking for the `Rest Client: Switch Environment`command, or by pressing `Ctrl+Alt+E`.
User settings stay on your machine unless you
enable Settings Sync; do not put these values in repository workspace settings.

For the webhook requests, send **Create Webhook** first and wait for a successful response. Then send **Get Webhook**;
its URL uses the `id` from the named `createdWebhook` response. If creation fails, the follow-up request cannot resolve
that response variable.

### 6. Send your first request

1. Open `trello.http` in Visual Studio or VS Code.
2. Select the `dev` environment (dropdown at the top right in Visual Studio, `Ctrl+Alt+E` on VS Code).
3. Click **Send Request** above `Who am I`. You should get a `200` with your Trello profile.
4. Run `Member's boards` to find the `id` of `Tandem Sandbox`, then continue with the other requests.

Example of a successful `Who am I` request:

200 OK

```json
{
  "id": ...,
  "aaId": ...,
  "avatarHash": ...,
  "avatarUrl": ...,
  ...
  "idBoards": [
    ...
  ],
  "idOrganizations": [
    ...
  ],
  ...
}
```

## Safety checklist

- [ ] Sandbox data only
- [ ] `git status` shows no `.user` or `.env` file before every commit
- [ ] Tokens revoked or rotated if they were ever exposed
