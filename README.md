# Create A React App With Vite

In this practice, you will

* Generate a React app using Vite
* Modify your app into a template that you can use more easily
* Create a README file using Markdown

## Generate a React app with Vite

Open a terminal and `cd` into the directory where you want to create your React
app.

To generate a new Vite project using their React template, run the following
command:

```sh
npm create vite@4.4.1 my-react-vite-template -- --template react
```

This will create a basic Vite React project named `my-react-vite-template`.
Let's break down that command.

`npm create` is an alias for [`npm init`]. It takes a package name (and optional
version with `@`) as an argument--here, `vite@4.4.1`--and runs `npm exec
create-<package-name>` under the hood. The first time you run it, you will
likely be asked if it is okay to install `create-vite@4.4.1`. Go ahead and
install it.

The next argument on the command line is the name of your project,
`my-react-vite-template`. The `--` then signals that any additional options
apply not to the Node command but to the script it is running. As a result,
`--template react` will be passed as an option to `create-vite`.

Once `create-vite` has finished creating your project--it shouldn't take very
long--enter the three commands that Vite tells you to run:

```sh
cd my-react-vite-template
npm install
npm run dev
```

`npm run dev` runs the `dev` script from your __package.json__; you will run
this command whenever you want to start your app. When it runs, you should see
something like this in the terminal:

```bash
VITE v4.5.0  ready in 592 ms

  ➜  Local:   http://127.0.0.1:5173/
  ➜  Network: use --host to expose
  ➜  press h to show help
```

> __Note:__ The `Local` line might also read `http://localhost:5173/` (which
> resolves to `http://127.0.0.1:5173/`).

Open a browser to [http://localhost:5173]. (Typing `o` in the terminal where
Vite is running will open a browser window to that address.) You should see
`Vite + React` underneath the two logos. Congratulations! You've just created
your first React app with Vite!

Before moving on, commit your code. Vite provides a __.gitignore__ file, but it
doesn't initialize the folder as a git repo. Do that now by running

```bash
git init
```

Now you can `git add` your initial files and commit them!

## Customize your a React app template

Your app now runs, but it has several elements that you won't need for your App
Academy projects (like the `Vite + React` home page!). There are also a few
features that would be nice to add. Fortunately, Vite is highly customizable.
Let's make this project into a reusable template. (Leave your app running while
you make these changes.)

Ignoring the __node_modules__ directory with all the installed dependencies, the
current file structure in the __my-react-vite-template__ folder should look like
this:

```plaintext
my-react-vite-template
├── public                  A directory to contain static images
│   └── vite.svg            The Vite logo
├── src
│   ├── assets              A directory to contain static images
│   │   └── react.svg       The React logo
│   ├── App.css             The App component's CSS
│   ├── App.jsx             The App component
│   ├── index.css           CSS for the entire site
│   └── main.jsx            The entry file, code that renders the App component
├── .eslintrc.cjs           ESLint configuration file
├── .gitignore              A standard .gitignore file tailored for React apps
├── index.html              The entry point to your app
├── package-lock.json       The package lock file
├── package.json            The package.json file
└── vite.config.js          Vite configuration file
```

Let's make some adjustments.

### Hot Module Replacement

The home page currently invites you to "Edit src/App.jsx and save to test HMR".
Let's start there. (HMR stands for _Hot Module Replacement_. It allows you to
refresh, add, or remove changed modules in a running app without having to
reload the whole app.)

Open __src/App.jsx__ in VS Code. First, let's make a small change to see HMR in
action. Delete the first `div`--including its contents, i.e., the two logos--in
the return statement and save the file. You should see the logos disappear from
your running app. Nice! If you check the terminal where your app is running, you
should also see a new message like this (obviously, the timestamp will be
different):

```bash
9:32:07 AM [vite] hmr update /src/App.jsx
```

There's not a huge performance savings here because your application at the
moment consists almost entirely of `App`, i.e., the updated module. As your
application grows, however, HMR will help ensure that a running app updates in a
timely manner whenever you change its source files.

### ESLint

Before you finish editing __App.jsx__, let's discuss linting. A _linter_ is a
tool that reviews your source code for programming errors, stylistic
inconsistencies, and suspicious constructs. **Always pay attention to your
linter.**

Vite includes the standard [ESLint] linter in your application. To run it,
simply open a new terminal and run `npm run lint`. If you do that now, it should
report that your app has two errors: `reactLogo` and `viteLogo` are "defined but
never used" in __App.jsx__. ESLint checks for, among other items, unused
variables that can clutter up and slow down your code. Because you deleted the
logo `div` in __App.jsx__ but left the two logo imports at the top of the file,
ESLint is now reporting that your code has two errors.

Let's fix the problem. Go back to __App.jsx__ and delete the two logo imports at
the top of the file. The terminal where your app is running should report
another "hmr update" because the source code has changed, but this change will
not produce any visible effect in the browser.

Return to the terminal with your linter and run `npm run lint` again. There
should be no more errors!

As you can see, linters provide very helpful feedback. Let's set the linter up
to run automatically whenever you build the app. To do this, you first need to
install an ESLint plugin by running:

```sh
npm install -D vite-plugin-eslint
```

Next, open __vite.config.js__. Add the ESLint plugin to the list of imports at
the top:

```js
// vite.config.js

import eslint from 'vite-plugin-eslint';
```

Then, in `defineConfig`, add `eslint` to the plugins array with the following
options:

```js
// vite.config.js

export default defineConfig(({ mode }) => ({ // <-- Change this line too!
  plugins: [
    react(),
    eslint({
      lintOnStart: true,
      failOnError: mode === "production"
    })
  ]
}))
```

**Note:** To access to the app's `mode`--i.e., whether the app is building for
`development` or `production`--you also need to convert what you pass to
`defineConfig` from an object to a function that receives `mode` from its
argument object. For more information, see [here][conditional-config].

The code above sets two options for ESLint. First, it sets `lintOnStart` to
true; this option enables the linter to run at build time. The second option,
`failOnError`, is set to false for the development environment. This ensures
that linter errors--like unused variables--will not prevent Vite from trying to
run your app.

Next, open __.eslintrc.cjs__ and add the following line under `rules`:

```js
// .eslintrc.cjs

  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
    'react/prop-types': 'off' // <-- Add this line
  },
```

Adding that line turns off the rule checking that you always validate the type
of your `props`. It's not a bad rule, it's just not a feature you will be using
in your App Academy projects.

Finally, while you are still in this file, add an `ignorePatterns` key with the
value set to an array containing `dist` and `node_modules`:

```js
  ignorePatterns: ['dist', 'node_modules'],
```

This tells the linter to ignore any files in __dist__--i.e., the folder where
Vite will store your production build by default--and __node_modules__.

Restart your app to load the new configuration. The linter should be happy now!

**Note:** You'll want to restart your app whenever you update the app's
configuration or __package.json__.

### Open your app in the browser automatically

As mentioned above, typing `o` in the terminal where Vite is running will always
open your app in a browser (or switch to your app if it is already open).

You can also have Vite automatically open your app in a browser window when it
first starts the server, a nice little convenience.

To add this feature, go back to __vite.config.js__ and define a `server` key
that points to `{ open: true }` in `defineConfig`:

```js
// vite.config.js

export default defineConfig(({ mode }) => ({
  plugins: [
    react(),
    eslint({
      lintOnStart: true,
      failOnError: mode === "production"
    })
  ],
  server: {
    open: true
  }
}))
```

**Note:** This option will be present but commented out in the App Academy
templates. To activate it, just uncomment the appropriate lines in
__vite.config.js__.

### Clean up files

The configuration is all set. Now you just need to clean up the code files.

Start by deleting the logo files that you no longer need. You can delete the
whole __src/assets/__ directory since it only contains __react.svg__. You do
want to keep the __public__ directory, however, as it has special functions in a
Vite app. (See [here][public] for more details.) Delete __public/vite.svg__ and
then add a new (empty) file in the __public__ directory named __.keep__. Since
git won't store an empty directory, the __.keep__ file is necessary to keep the
(otherwise empty) __public__ directory as part of your project when you push it
to GitHub. (The solution template will include an a/A [favicon] here.)

Delete __App.css__. (Your page will now crash because __App.jsx__ can no longer
import __App.css__. You will fix that in a moment.)

The rest of the files you just need to tweak a bit. Let's start with
__index.html__ since that is your app's entry file. You only need to make two
changes here. First, make the `title` "App Academy Template" or "My Template"
instead of "Vite + React". Second, since you've removed the __/vite.svg__ icon
from the __public__ folder, delete the link that tries to access it:

```html
<link rel="icon" type="image/svg+xml" href="/vite.svg" />
```

While you're in __index.html__, look at the `script` in the `body`. It loads
__src/main.jsx__ and designates it as type `module`. This is how your app gets
loaded into the browser.

Next, open __src/main.jsx__. This is the component that renders your app into
the `root` element. Nothing has to change here, although you can delete the
extraneous `,` after `</React.StrictMode>`.

Returning to __App.jsx__, the default `App` does a great job promoting Vite and
React and setting up a basic counter with the `useState` hook. It is not a
particularly helpful base for building other projects, however. Delete
everything and replace it with this simple `App`:

```jsx
// src/App.jsx

function App() {
  return <h1> Hello from App </h1>;
}

export default App;
```

Finally, __index.css__ contains sitewide styles. At the moment, it is full of
styles intended for an `App` component that you largely just deleted. Go ahead
and delete everything in this file, too. Add a comment at the top that says
simply, `/* TODO: Add sitewide styles */`.

That's it! If your server is not already running, run `npm run dev` again to
make sure everything works. If you see a page that says, "Hello from App", then
you've successfully set up your template!

## README.md

As a final step, let's create a README!

In a code repo, a README serves to introduce interested parties to the codebase.
A good README will typically explain

* The purpose of the code / repo
* How to download, clone, or otherwise access the codebase
* How to set up / install the project
* How to use the project
* How to contribute or make changes to the codebase

### Markdown

READMEs are written in a language called [Markdown]. A few Markdown tips:

* Use a `#` at the beginning of the first line to indicate the page title;
  create subsequent (sub-)headings by adding additional `#`s--e.g., `##` or
  `###`--to indicate the heading level.
* Put empty lines on either side of a heading (except above the first line of a
  file).
* Use single underscores(`_`) or stars(`*`) around anything that should be in
  italics: `_this_` = _this_.
* Use double underscores(`__`) or stars(`**`) around anything that should be in
  bold: `**this**` = **this**.
* Use backticks to surround code, like \`this\` (= `this`).
* Use three backticks on a new line to signal the beginning and end of a code
  block. You can specify the language after the opening backticks, e.g., ` ```js
  ` or ` ```plaintext `.
* Add **two spaces** to the end of a line if you want Markdown to respect the
  line break.
* In VS Code, to preview how a Markdown file will appear when processed,
  right-click the file and select `Open Preview`.

  > **Note:** Markdown comes in different _flavors_, or versions, that can
  > differ in little ways with regard to syntax and behavior. GitHub, in
  > particular, has its own flavor, _GitHub Flavored Markdown_, or GFM for
  > short. As a result, what you see in VS Code may not look exactly like what
  > you will see on GitHub. In most cases, however, the two are close
  > enough to make VS Code's flavor a good approximation.

### Writing the README

To begin, create a __README.md__ file in your root directory. Add an appropriate
title on the first line, something like `My React Vite Template`. (Remember to
use a single '#'!)

Skip a line after the title, then provide a brief summary of the repo's purpose
(i.e., what the code does and any particular use cases).

Since this is a template repo, you should probably tell users how to clone it
next. Skip another line and make a new subheading, `How to clone`. (Remember to
put another blank line after the subheading.)

The command that you will run to clone the repo is this:

```sh
npx tiged <YOUR-GH-USERNAME>/my-react-vite-template#main <new-project-name>
```

(You can substitute your GitHub username for `<YOUR-GH-USERNAME>`.)

This command will create a clone of the repo' `main` branch under the name
<new-project-name> in the directory where it is run.

Write a brief section that highlights this command as the way for users to clone
the repo. **Tip:** You might find code-block indicators to be helpful here.

Next, add another section (with appropriate subheading) that tells users how to
install (`npm install`) and run (`npm run dev`) the project. You also want to
make sure that users are running these commands in the correct directory.
**Hint:** Think about the message Vite gave you after it created your project.
(Look back near the beginning of these instructions if you don't remember what
that message was.)

Finally, make a note somewhere--either in a new section or as part of the one
above--that reminds users what files they will need to change to convert the
template fully into their new project, namely, this README, the `title` in
__index.html__, and the `"name"` in __package.json__.

(If you want, you can also add a section on how to contribute to the repo.)

When you finish, open the `Preview` view of your file in VS Code to verify that
you have formatted everything correctly in Markdown. Adjust as needed.

That's it! Commit and push your code to a remote repo. You now have a functional
Vite React template repo!

[`npm init`]: https://docs.npmjs.com/cli/v9/commands/npm-init
[http://localhost:5173]: http://localhost:5173
[conditional-config]: https://main.vitejs.dev/config/#conditional-config
[ESLint]: https://eslint.org/
[favicon]: https://developer.mozilla.org/en-US/docs/Glossary/Favicon
[public]: https://vitejs.dev/guide/assets.html#the-public-directory
[Markdown]: https://www.markdownguide.org/getting-started/
