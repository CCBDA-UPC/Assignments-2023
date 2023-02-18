# Lab session #1: Basic "Knowledge Toolbox" to get started in the Cloud
In this Lab session, you will be asked to put into practice the basic knowledge required for the Lab sessions of this course.


#  Pre-lab homework 0
Take a look at the following hands-on guides to check if you already have the basic knowledge to follow this course. If not, please do the assignments.

* Hands-on 1: [Git and GitHub Quick Start](../../../Cloud-Computing-QuickStart/blob/master/Git-Github-Quick-Start.md)
* Hands-on 2: [Markdown syntax](../../../Cloud-Computing-QuickStart/blob/master/Quick-Start-Markdown.md)
* Hands-on 3: [Python Quick Start](../../../Cloud-Computing-QuickStart/blob/master/Python-Quick-Start.md)
* Hands-on 4: [Python Development Environment Quick Start](../../../Cloud-Computing-QuickStart/blob/master/Python-Development-Environment-Quick-Start.md)

#  Pre-lab homework 1
Go to the AWS Academy and log into the course [AWS Academy Cloud Foundations](https://awsacademy.instructure.com/courses/40548)

Follow the modules and submit the knowledge checks at the end:
- Module 1 - Cloud Concepts Overview
- Module 2 - Cloud Economics and Billing

#  Tasks for Lab session #1
## Task 1.1:
Install Python on your laptop.

Create a folder for the assignment `Lab1` and install a Python virtual environment under a folder named `Lab1/venv`

Activate the virtual environment and install the necessary pip packages.

[PyCharm](https://www.jetbrains.com/pycharm/) is a very popular [IDE](https://en.wikipedia.org/wiki/Integrated_development_environment) that will make your life much easier. It supports execution and debugging of Python, Python environments, code version control, it has a built-in terminal and all kinds of plugins. Moreover, it is [completely free for students](https://www.jetbrains.com/buy/classroom/?product=pycharm).

You can [create a new PyCharm project](https://www.jetbrains.com/help/pycharm/creating-and-managing-projects.html) and [Configure a virtual environment](https://www.jetbrains.com/help/pycharm/creating-virtual-environment.html)


## Task 1.2:
Create a python code that uses the “random” library. Build a program in python that generates a random number between 1 and 20. Then let the player guess the number entered, displaying if the number is too low or high. The game ends either when the number is guessed correctly. The suggested program name is `Lab1.guessnumber.py`.

If you are using PyCharm try to become familiar with the integrated debugger. You will need to debug your code in future sessions. On the top-right part of the IDE:

<p align="center"><img src="./images/Lab01-PyCharmEditConfig.jpg " alt="Edit configuration" title="Edit configuration"/></p>

Create a new configuration to run each Python Script:

<p align="center"><img src="./images/Lab01-PyCharmDebugConfig.png " alt="New configuration" title="New configuration"/></p>

Just to become familiar with the IDE, set some break points and examine the variables.


## Task 1.3:

Use the `private repository` **https://github.com/CCBDA-UPC/2023-1-xx** that you have received where you and your team partner need to leave your responses. **(Replace xx using the repository name that you have been asigned)**.

Populate the `private repository` with the contents that you have just cloned.

## Task 1.4:   
Update your remote repository from the local repository on your laptop:
```
echo "# 2023-1-xx" >> README.md
git init
git add README.md
git add Lab1.guessnumber.py
git commit -m "first commit"
git remote add origin https://github.com//CCBDA-UPC/2023-1-xx.git
git push -u origin master
```
> change `<username>` for your Github account

It is better that you manage git by hand. Once you become familiar with git you can use PyCharm to save you some typing.

## Task 1.5:
Update the `README.md` file including all the information about your group (member's name and email addresses).
## Task 1.6:
Create an EC2 instance at AWS. Login and pull down all the contents of your GitHub repository to make an exact clone by using `git clone` command.
## Task 1.7:
Execute the program `Lab1.guessnumber.py` in your AWS instance. Take a screenshot of the terminal window that are you using as proof.
Include that screenshot in your local repository, on your laptop, with the name `Lab1.AWSterminal.png`.
Update your `README.md` file to make that screenshot appear.
## Task 1.8:
Update your remote GitHub repository with the updated `README.md`and the new file `Lab1.AWSterminal.png` using the `git`commands `add`, `commit` and `push`.
## Task 1.9:
Don't forget to **individually** submit the knowledge check of the AWS Academy course.

**Questions: How long have you been working on this session? What have been the main difficulties you have faced and how have you solved them?** Add your answers to `README.md`.


# How to submit this assignment:

Push to the repo, at least, the files `README.md` with your responses to the above questions.

Include the code and screenshot for task 1.7

Make sure that you have updated your local GitHub repository (using the `git`commands `add`, `commit` and `push`) with all the files generated during this session.


**Before the deadline**, all teams shall push their responses to their private **https://github.com//CCBDA-UPC/2023-1-xx** repository.