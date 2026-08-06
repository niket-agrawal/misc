### Index
- ##### [Lesson 1 - Using Virtual Environment Module in Python](#lesson-1---using-virtual-environment-module-in-python-1)
- ##### Lesson 2 - 

<br/><br/><br/>

# Lesson 1 - Using Virtual Environment Module in Python

[venv](https://docs.python.org/3/library/venv.html) is an inbuilt Python module to deal with virtual environments during product development.

## Installation

In-built module with Python 3.3 onward. To check, type `python -m venv` in command line. If following appears, then go ahead with using it.

```bash
usage: venv [-h] [--system-site-packages] [--symlinks | --copies] [--clear]
            [--upgrade] [--without-pip] [--prompt PROMPT]
            ENV_DIR [ENV_DIR ...]
venv: error: the following arguments are required: ENV_DIR
```

## Usage

Using `venv` module usually consists of 5 steps - Creation, Activation, Actual Use, Deactivation and Deletion. I've summarized each step with its basic commands.

### Step 0 - Recommendations

> Before playing with virtual environments, it is highly recommended to make a separate directory to store all your new environments.

There are 2 ways to do this.

```bash
Way 1 - for Environment Oriented Coding
      - Create a "New Folder" e.g. "Niks_virtual_envs"
      - cd inside this folder, and proceed with *Step1* to create new environments here.
      - Renaming new environment according to your project e.g. "env_proj1" is recommended.

Way 2 - for Project Oriented Coding
      - Create a "New Folder" e.g. "Niks_projects"
      - Again, create "New Folder" inside this project folder and name it for e.g. "Project1"
      - Now, cd inside this "Project1" folder, and proceed with *Step1* to create new environment here.
      - Renaming new environment "venv" is recommended.

```

### Step 1 - Create New Environment

General usage: `python -m venv new_venv`

If you followed Way 1 - 
```bash
niks@home > mkdir Niks_virtual_envs
niks@home > cd Niks_virtual_envs
niks@home\Niks_virtual_envs > python -m venv env_proj1
niks@home\Niks_virtual_envs > dir
```
If you followed Way 2 - 
```bash
niks@home > mkdir Niks_projects
niks@home > cd Niks_projects
niks@home\Niks_projects > mkdir Project1
niks@home\Niks_projects > python -m venv Project1\venv
niks@home\Niks_projects > dir Project1
```

### Step 2 - Activate your Environment

Navigate to directory containing your environment and type: `new_venv\Scripts\activate.bat`

for e.g. if you followed Way1 - 
```bash
niks@home\Niks_virtual_envs > env_proj1\Scripts\activate.bat
(env_proj1) niks@home\Niks_virtual_envs >
```
or if you followed Way2 - 
```bash
niks@home\Niks_projects > cd Project1
niks@home\Niks_projects\Project1 > venv\Scripts\activate.bat
(venv) niks@home\Niks_projects\Project1 >
```

### Step 3 - Actual Use

Haha are you kidding, do `pip list` and `where python` and go ahead. Make something awesome.

To save your environment dependencies for distribution in the form of `requirements.txt`, use this command.

```bash
(venv) niks@home\Niks_projects\Project1 > pip freeze > requirements.txt
```
After distribution, if you want to install all dependencies on host, use this command. (After navigating the directory for `requirements.txt` file.
```bash
(new_host_venv) host@home > pip install -r requirements.txt
```

### Step 4 - Deactivate your Environment

Deactivating your virtual environment is fairly simple, just write `deactivate` 

```bash
(venv) niks@home\Niks_projects\Project1 > deactivate
niks@home\Niks_projects\Project1 >
```

### Step 5 - Delete your Environment

Deleting the environment is as simple as deleting your environment directory. Just go to respective directory, if you did started with **Way 1** - write `rmdir env_proj1`, or for **Way 2** - write `rmdir venv`. 

```bash
niks@home\Niks_projects\Project1 > rmdir venv /s
Are you sure (Y/N)? y
```

## Environment with access to Global Packages

General usage: `python -m venv new_venv --system-site-packages`, for creating virtual environment that has access to global version of Python packages.

Local packages installed on this virtual environment will not affect any package globally. To see the list of local packages inside any environment, use `pip list --local`.

## Nuance: Way 1 v/s Way 2

>Way 1 is preferred generally when your `environments` are separately stored from the `projects` directory. `You can activate whichever environment you want to work with your project.` That's why, I called it "Environment Oriented".

>Way 2 is used when your `projects` directory have its own `venv` and `requirements.txt` for cross-platform distribution. Here `environment` is stored within your `project` directory. `You can navigate to your project folder and activate its venv before working on that particular project.` That's why, I called it "Project Oriented".



## References

- [Corey Schafer Tutorial](https://www.youtube.com/watch?v=APOPm01BVrk)
- https://enterprise.github.com/downloads/en/markdown-cheatsheet.pdf
- https://pandao.github.io/editor.md/en.html
- https://www.makeareadme.com
- https://jbt.github.io/markdown-editor/
