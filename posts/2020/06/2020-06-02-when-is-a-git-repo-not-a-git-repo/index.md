# When Is a Git Repo Not a Git Repo?


I was working on a project recently and I noticed that my git gutter wasn't showing up in VS Code.

Quick side note, for those that don't know, a git gutter, diff decorator or VCS gutter is a small element that typically shows up on the side of your code files to indicate a change between your local copy of the file and that which the version control system is tracking.

They look a little like this

{{< image src="/posts/images/2020/06/02/demo_gutter.gif" >}}


Back to the problem, I double, triple and quadruple checked my settings and extensions and couldn't find anything amiss. I was perplexed, so I opened a new file within the same project and made some changes but nothing...Ok, what about a new project, open it up make a change and a wild git gutter appeared!

Ok, so the same editor two different projects and I get two different results. So what was different about them? They both look like git repos in my terminal, behave like git repos when it comes to making changes, was it a language issue, the size of the repo, too many branches?

There were too many variables, so I decided to start a fresh and see if I can pinpoint the issue.

```
for i in foo bar; do mkdir ~/goatspace/$i; git init ~/goatspace/$i; done
```

{{< image src="/posts/images/2020/06/02/fresh_repos.png" >}}

Right, we have our test repos, time to add them to VS Code as projects, I use the excellent extension [Project Manager](https://marketplace.visualstudio.com/items?itemName=alefragnani.project-manager), so I opened up the extension settings and added links to my two newly initialised git repos.

```
[
	{
		"name": "Foo",
		"rootPath": "/Users/pavdjian/goatspace/foo",
		"paths": [],
		"group": "",
		"enabled": true
	},
	{
		"name": "Bar",
		"rootPath": "/Users/pavdjian/gitspace/bar",
		"paths": [],
		"group": "",
		"enabled": true
	}
]
```

For the astute amongst you, you'll notice two different `rootPaths`.
```
» ls -lah bar
lrwxr-xr-x  1 pavdjian  pavdjian    29B  2 Jun 22:34 bar -> /Users/pavdjian/goatspace/bar
```

This setup difference was by design to mimic the setup of the projects I was comparing against earlier. Time to see if any git gutter decorations show up in these new test repos.

```
for i in foo bar; do echo "this is a test" >> ~/goatspace/$i/test.txt; done
```

Looking at our editor, we can see the window tracking the `Bar` project is not showing any file highlighting.

{{< image src="/posts/images/2020/06/02/untracked_file.png" caption="Untracked Files (`image`)" >}}

I should state at this point neither file is showing a git gutter, but that's expected at this point since the files are untracked as far as git is concerned. Let's make a quick commit

```
for i in foo bar; do cd ~/goatspace/$i; git add .; git commit -m "New file"; done
```

Then we'll edit the files again

```
for i in foo bar; do echo "this is a second test" >> ~/goatspace/$i/test.txt; done
```
And now we see a git gutter appear in the `Foo` project window, but not in the `Bar` project.

{{< image src="/posts/images/2020/06/02/git_gutter.png" >}}


So it seems, VS Code doesn't show the git gutter when the file that it is opening is coming from a symlink. If I was to open the file from the hard linked `goatspace` directory though

```
code ~/goatspace/bar
```
All the wonderful git decorations are present and working.
{{< image src="/posts/images/2020/06/02/goatspace_hardlink.png" >}}


For what it's worth, I've not seen this behaviour before, vim for instance seems happy to show the decorations whether the file is opened via symlink or not.

{{< image src="/posts/images/2020/06/02/vim_example.png" >}}

And with that, thanks for reading.

