# How to contribute

You need python for this. I know, I am not a cool kid with go.
Any refactor you want to use hugo is welcomed. It's just easy for me to use
sphinx out of habit.

## Step 1: Package install

Ensure you have your system's python pip package installed.

## Step 2: Setup tooling

Then use: `pip install --user -r dev-requirements.txt`.
You should have a binary in your local user space. Make sure your $PATH is
properly set. You should be able to run the "tox" command from now on.

## Step 3: Run your action with tox.

### Building docs

`tox -e docs`

This builds the docs in docs/build/html.

To read them:

* locally, run `xdg-open docs/build/html/index.html`.
* remotely, go into your built folder `docs/build/html`,
  then run `python3 -m http.server`. Point then your browser to your machine,
  like `http://127.0.0.1:8000`.

The docs source is in docs/source/ (obviously?!).

If you are not familiar with restructured text, please have a looksie in here:
https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html

## Step 4: Prepare your commit

This repo uses semantic commits. If you write your commit well, there is
_nothing_ to be done for releasing.

Here is a link to the information you need to put in your commit message.

https://www.conventionalcommits.org/en/v1.0.0/#summary

Don't forget that commits should be explaining the context of the WHY
you do something.
