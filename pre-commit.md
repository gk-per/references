# Pre-commit hook for mix format

To set up a pre-commit hook for mix format in an Elixir project, you need to create a script that runs mix format before each commit and add it to your project's .git/hooks directory. Here's a step-by-step guide:
1) Navigate to your Elixir project directory: 
```
cd /path/to/your/elixir/project
```
2) Create a new file named `pre-commit` in the `.git/hooks directory`: 
```
touch .git/hooks/pre-commit
```
3) Open the `pre-commit` file in your favorite text editor and add the following script:

```bash
#!/bin/sh

# Check if mix is available
if ! command -v mix &> /dev/null
then
    echo "mix (Elixir) is not available. Please install Elixir to use this pre-commit hook."
    exit 1 
fi

# Run mix format
echo "Running mix format..."
mix format

# Check if there are any uncommitted changes after running mix format
if ! git diff --quiet
then
    echo "There are uncommitted changes after running mix format. Please review and commit them."
    exit 1 
else
    echo "No changes after running mix format."
fi
```

4) Make the `pre-commit` file executable:

```
chmod +x .git/hooks/pre-commit
```

Now, every time you commit changes to your Elixir project, the `mix format` command will be executed, and if there are any formatting changes, the commit will be aborted, allowing you to review and commit the changes made by `mix format`.