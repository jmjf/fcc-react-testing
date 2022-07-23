# First test application

Course intro describes three applications. The first will end up deploying to Azure, which I may skip for now.

This app isn't going to do much, mainly serves as a basis for learning testing.

The good news is, he seems to be started from am empty directory for this one, so hopefully will give better guidance. He is using React 17 (based on his GitHub repos), and isn't using TypeScript, so I may end up translating some things. Still, past experience with R17 and TS says that probably won't be a major issue.

## Create a working environment

```bash
npx create-react-app test-app --template typescript
cd test-app
npm start
```

He does `npm i create-react-app`, but I've read just `npx` is the preferred method, so that's what I'm using.

We should get the default React "hello world" app on `localhost:3000`.

## First test

CRA creates a basic test in `App.test.tsx`

- `npm test` to run it and see it pass
- Read the test to understand what it's doing (renders `App` and confirms an element contains some text)
- Note it's using `@testing-libraru/react`, which adds assertions for jest like `toBeInTheDocument()`

**COMMIT: 1.0.1 - CHORE: setup application, ensure it runs, ensure test runs**

## CI pipeline

He's using GitHub actions, so start in the GitHub repo

- Actions tab
- Search for Node.js workflow (build and test a Node.js project with npm)
- Configure it to see the UI on GitHub

He doesn't seem to save this, but writes his own. I'm going to modify in place and save.

- `env` defines environment variable values to use
  - If values are secrets, add in GitHub Settings, Secrets, Create repository secrets
  - I set up the three secrets with initial value `tbd` because I don't know what these are yet
- `on` defines when to run actions
  - Perform actions for pushes and pull requests to the main branch (no other branches)
- `jobs` defines the jobs to execute
  - We're defining a job called `build`
  - `runs-on` OS to use (`ubuntu-latest` in this case)
  - `strategy` defines build tool version(s) to use (node 16.x for me)
  - `steps` defines what the job does
    - checkout the code
    - pick a node version from `strategy`
    - setup node for that version
    - install dependencies
    - he breaks down the default steps with descriptive names to make it easier to follow
    - `npm ci` is similar to `npm install` but caches dependencies so run faster
    - runs tests
    - ends by running the application for 60 seconds

**BUT DON'T SAVE FROM GITHUB**
Instead, create `test-app/.github/workflows/test-app-ci.yml` and save it there (I think)

```yml
# test-app-ci.yml
# Workflow for test-app based on Node.js workflow

name: test-app ci

env:
  SCREENER_API_KEY: ${{ secrets.SCREENER_API_KEY }}
  SAUCE_USERNAME: ${{ secrets.SAUCE_USERNAME }}
  SAUCE_ACCESS_KEY: ${{ secrets.SAUCE_ACCESS_KEY }}

on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [16.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

    steps:
      - uses: actions/checkout@v3
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
          cache: "npm"
      - name: Install dependencies
        run: |
          cd test-app
          npm ci
      - name: Build the app (production version)
        run: |
          cd test-app
          npm run build
      - name: Run component tests
        run: |
          cd test-app
          npm run test
      - name: Start the app
        run: |
          cd test-app
          npm start &
          npx wait-on --timeout 60000
```

If this works, it should run the CI pipeline on push to GitHub.

**COMMIT: 1.0.2 - CI: add GitHub action to run tests**

Hmm. Now he's talking about a new branch, which I may have missed??? I'll create a branch and see what happens when I push it.

**COMMIT: 1.0.3 - CI: create a branch to test CI and push it**

Okay, now I see the branch on the Pull requests tab. So, Compare & pull request it.

AH! Now he notes he should have put the workflow in the root, not in `test-app`. Let's move it and try again.

- Merge the branch in GitHub
  - create PR
  - see prompt to set up CI, which is what signaled CI was in the wrong place
  - merge it
- On local, get changes
  - switch to `main` branch
  - pull from the origin to sync it

Now commit to move CI to the correct location and commit.

**COMMIT: 1.0.4 - CI: move ci file to the correct location**
