# Reverse engineering lab for classrooms: read programs with gdb, radare2, strace and ltrace

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml)

A ready-made **reverse engineering lab** for teaching how to read a program that has no source code, from the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning), where schools and colleges give every student their own machines and terminal in the browser. Each student gets a free-to-try Ubuntu **VM** with a compiler, **gdb**, **radare2**, binutils, **strace** and **ltrace**, and five small programs written for the lab, each hiding a key. They find the keys by looking at the programs, as a defender or a student of compilers would, and write them in a file. Nothing is installed on the student's computer.

This is reading and understanding only, on programs the lab compiles itself. It does not teach malware, breaking the licence of real software, key generators, hiding a program from a debugger, exploits, or looking at any program that is not the lab's own.

## What the student does

The lab puts five programs in `~/revlab` (`hello`, which has no key and is for practice, then `crack1` to `crack5`), an `answers.txt` and a `README.md` in the home folder that says what to do and gives none of the answers. Each program prints only `Access granted.` or `Access denied.`:

1. `crack1` takes a password on the command line: look without running it (`file`, `strings`, `objdump`, `r2`).
2. `crack2` takes a password on the command line: stop it while it runs in `gdb` and look in memory.
3. `crack3` takes a number: read the arithmetic in the disassembly and work backwards.
4. `crack4` asks for a password when it starts: watch the library calls it makes with `ltrace`.
5. `crack5` looks for a file: watch the system calls it makes with `strace`.
6. Write each key in `answers.txt`, one line each, in the form `crack1=the-key`.

Behind it is the lesson a defender needs: whatever a program must know in order to work, a reader can find, so a secret kept in a program is not a secret.

## A different key on every copy

The repository is public, so no key is in it. When a student's VM first starts, `setup` makes new random keys, stores them in a folder only root can enter, builds the programs with them and deletes the sources. The keys are random, not planted fixtures. A student's answers are checked against their own copy's keys, so a classmate's answers or the lab's files give nothing away.

## How it is marked

The lab has a **check**, a script that marks a handed-in VM without the teacher reading it. It runs on a copy of the saved machine after a fresh start and reads `answers.txt` in the student's home folder. Each of the five keys must be the key made for that copy of that program, so a guess, an empty line, another copy's key or a key put against the wrong program does not pass. It also runs every program with its own key, so a program that was changed is noticed. The first line of what it prints says what is not right.

What it does not do: it marks the answers and not how they were found, and a student who uses `sudo` on their own VM could read the stored keys. The lab trusts a student with their own machine. It is a check for a classroom, not a proof.

## What is in the file

`reverse-lab.lab.yaml` is a whole **VM lab definition**, exactly what the platform runs:

| Key | What it is |
| --- | --- |
| `cpus`, `memory`, `disk` | the size of each student's VM: 1 CPU, 1024 MB and 10 GB |
| `open` | the ports that open in the browser: none, the lab is the terminal |
| `setup` | a script run once, as root, the first time each student's VM starts: it installs the compiler and tools from Ubuntu's archive, makes the keys, builds the five programs, and writes `answers.txt` and the README |
| `check` | the marking script, run as root on a copy of the saved disk after a fresh boot; its exit status is the result and `0` passes |

## Using it on the platform

**To set it as work for a class:**

1. In your module, add a task and choose the **Ubuntu VM lab** card.
2. Press the Reverse engineering sample, or paste the contents of `reverse-lab.lab.yaml`. Using a sample replaces the text, and Undo brings yours back.
3. Publish and try your own copy first. Every student gets a VM of their own, kept between sessions, with keys of their own.
4. Assign it like any task. When it is handed in, run the check on a student's row in the class list: it starts a copy of their saved machine that is not saved back, and shows whether it passed and what it printed.

A Basics lesson on the platform works in this lab. The help manual's [page for this lab](https://mitsurutech.co.uk/help/lab-reverse-lab), its [guide to Ubuntu VM labs](https://mitsurutech.co.uk/help/labs) and its [sample labs page](https://mitsurutech.co.uk/help/sample-labs) cover the rest.

No college account yet? [Your first year is free](https://mitsurutech.co.uk/pricing) for up to 50 students, and [signing up](https://mitsurutech.co.uk/signup) takes an email address.

## Checking the file

On every push, `.github/workflows/check.yml` checks that the file is valid YAML with the keys above and that `setup` and `check` are each inside the platform's 16 KB limit. It does not boot a VM: that is done on the platform's own machines.

## What does not work in a lab

The VM reaches Ubuntu's package archive, PyPI, npm and GitHub, and nothing else. This lab needs only the archive, and a student's copy cannot reach the internet to fetch any other program. Setup takes a few minutes the first time, while the tools install.

## About

Made by [Mitsuru Technologies](https://mitsurutech.co.uk) for the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning): Python, Java, Go, Node, SQL and Linux in the browser for schools and colleges, with container labs, VM labs, web apps deployed from GitHub, and quizzes that mark themselves. Try code with no account in the [playground](https://mitsurutech.co.uk/playground).

More labs from the same place: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) and [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Licence

This work is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). You may share it and adapt it for learning and teaching, if you credit Mitsuru Technologies Ltd, do not use it commercially, and share anything you make from it under the same licence. The full text is in the `LICENSE` file.

The compiler, gdb, radare2, binutils, strace and ltrace are installed from Ubuntu's archive when a VM starts; nothing of them is distributed here.
