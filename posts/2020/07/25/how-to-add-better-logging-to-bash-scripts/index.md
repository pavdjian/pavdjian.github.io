# How to Add Better Logging to Bash Scripts


If I were to look for a site reliability / infrastructure / devops engineer job
one of the skills that will likely be mentioned across a large number of specs
would be "scripting". Next to it, you'll likely see the word "bash".

It is probably the scripting language that I've used most frequently, both
professionally and personally, in that time I've found a few ways to make my scripts
a little more robust and user friendly.

### What's a Bash Script?
Before we go any further, we should tackle the question of what a bash script actually is.
Now, there is a lot of [documentation](https://links.avdjian.com/shell-scripts) out
there which will explain things better than I could, but for the purposes of this post,
I like to think of bash scripts as:

A plain text file containing any series of commands that can be run on the command line.

It's probably worth noting that in order to execute any of the commands contained
within the file, you need to ensure your file has the execute permission enabled,
this can be achieved pretty simply with `chmod +x <file_name>`

### Better Logging
When writing scripts it's always a good idea to add some [safeguards](https://links.avdjian.com/bash-guards)
to ensure that an error in an earlier command does not cascade through the
script resulting in unexpected behaviour.
Assuming that appropriate safeguards have been added, I've found including a few
helper functions to handle logging can go a long way where user experience is
concerned.

Below is a small snippet I like to add to my scripts

```
RED="\\033[31m"
GREEN="\\033[32m"
YELLOW="\\033[33m"
RESET="\\033[0m"

function log_step {
    echo -e "${GREEN}$*${RESET}"
}

function log_warning {
    echo -e "${YELLOW}$*${RESET}"
}

function log_error {
    echo -e "${RED}$*${RESET}"
}
```
It sets up some colour variables, and then creates a few functions which can
be used later in the script to `echo` outputs to the end user in the colour(s) specified.

I've settled on the use of `GREEN`, `YELLOW` and `RED` as they do a good
job of communicating, errors, warnings, prompts and transitions between commands,
however, you can always add or change the colours by [updating the colour codes](https://links.avdjian.com/terminal-colours)
used.

Below I've included a sample script showing how my snippet works in practice:

```
RED="\\033[31m"
GREEN="\\033[32m"
YELLOW="\\033[33m"
RESET="\\033[0m"

function log_step {
    echo -e "${GREEN}$*${RESET}"
}

function log_warning {
    echo -e "${YELLOW}$*${RESET}"
}

function log_error {
    echo -e "${RED}$*${RESET}"
}

log_step "This is the first step of my script"
log_warning "Sometimes, we'll throw a warning or prompt for the user"
log_error "Othertimes, we'll want to throw an error"
```

When the above is run, you see something similar to this:
{{< image src="/posts/images/2020/07/25/terminal.png" >}}

And with that, thanks for reading.

