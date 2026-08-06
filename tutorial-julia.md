# Lesson 1 - Using Virtual Environment in Julia

[pkg](https://pkgdocs.julialang.org/v1/environments/) is Julia’s built-in package manager and handles operations such as adding, updating and removing packages. Pkg has it’s own read — evaluate — print — loop (REPL).

## Installation

In-built module with Julia. To check, type `]` in julia command line. If following appears, then go ahead with using it.

```bash
               _
   _       _ _(_)_     |  Documentation: https://docs.julialang.org
  (_)     | (_) (_)    |
   _ _   _| |_  __ _   |  Type "?" for help, "]?" for Pkg help.
  | | | | | | |/ _` |  |
  | | |_| | | | (_| |  |  Version 1.10.2 (2024-03-01)
 _/ |\__'_|_|_|\__'_|  |  Official https://julialang.org/ release
|__/                   |

(@v1.10) pkg>
```

## Usage

Using `pkg` module usually consists of 3 steps - Generation, Activation and Actual Use. I've summarized each step with its basic commands.

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
```

### Step 1 - Generate New Environment

General usage: `generate env_name`

If you followed Way 1 - 
```bash
niks@home > mkdir julia_envs
niks@home > cd julia_envs
niks@home\julia_envs > generate env0_defaultJulia
```
If you followed Way 2 - 
```bash
niks@home > mkdir Niks_projects
niks@home > cd Niks_projects
niks@home\Niks_projects > mkdir Project1
niks@home\Niks_projects > generate Project1/env0_defaultJulia
```

### Step 2 - Activate your Environment

Type: `activate env_name`

for e.g.
```bash
(@v1.10) pkg> activate env0_defaultJulia
  Activating project at `C:\programs_pkgs\julia\env0_defaultJulia`
```

If you want to activate the environment in different directory
```bash
> cd what/ever/directory/you/want/to/go
> julia
julia> import Pkg;
julia> Pkg.activate("C:\\programs_pkgs\\julia\\env0_defaultJulia")
julia> Pkg.status()
```

### Step 3 - Actual Use

Haha are you kidding, do `status -m` to see recursive dependencies of all pakages. Make something awesome.

To add new packages to environment, in pkg REPL, type `add Pluto`
