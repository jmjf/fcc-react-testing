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
