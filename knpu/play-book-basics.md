# Playbook Basics

So at this point we can execute modules on hosts, but we're still doing it from the command line. Obviously that's not a good long term recipe. What we want to do is create a big configuration file that describes all of the modules that we need to execute to get an entire server set up, like installing PHP, installing MySQL, installing Nginx, configuring everything.

The way it's done is in something called a playbook, which is yml formatted file, which contains these instructions. So in your Ansible directory, we're going to create a new file called playbook.yml and for now, just leave that empty.

To execute this, instead of using the Ansible command, use Ansible-playbook. Point that at your playbook and then still need the -i to point at our host .ini file. If you run it now you're gonna get an error that says "playbook must be a list of plays" so there's a bit of terminology with the playbook.

First, let's look at what a simple playbook looks like. Every file in Ansible always starts with three dashes. It's not important. It's there for reasons related to yml just make sure you have it. It's all good. Below that we're gunna say "host: all" Then we're gonna indent two and say "tasks" then indent again say "ping: tilbay".

If you are new to yml you do need to watch out on the spacing inside of this file. It is important. Now we're saying here is, on all of our hosts in our host file, so all is a special string here, we're going to run the ping module and this time when we run it, it works. You can see it runs the ping against our 127 local host and also against our Ansible machine.

So inside of a playbook - this is a playbook - you have plays. A playbook contains plays. In this case we have a single play that will run on all host. You could later have another host line down here that runs on only some hosts and that entire section would be it's own play. So for this tutorial were gonna creating a playbook with a single play, but if you need to execute different things against different hosts, each group would be it's own play.

So a playbook contains plays. Our one play contains tasks and tasks execute modules. So in this case we're executing the ping module and as we build out this file that structure is gonna become more and more obvious. So if you don't want to run this against all of your hosts, there are two options. The first is at the command line you can pass "-lvb" to only run against the virtual box host. Perfect. But a better way is actually inside of your playbook. We know that the code were gonna put down here; the tasks we're gonna list in here are here to setup a virtual machine so we don't want these to run on a local machine ever, so we're going to change the host here to be "vb". And now without that "-lvb" flag we're gonna get the same result. It's only gonna run our tasks, in this case our ping task against the virtual machine.

And that is the basics of your playbook. Most of the work your gonna be doing is gonna involve adding more and more tasks; different things that you want to happen on that machine. Probably the most common one is using a module call At. As the name suggests this is something that helps you install "at git" packages on Debian or Ubuntu.

So let's look at an example. Usually a task is when I have this format. You actually give it a name. We're going to use "at-git" to install a really important utility called Cowsay. Now if you've never seen Cowsay, I have it installed locally. Once you have it installed you can say "Cowsay" give it a message and it prints out a cow that's saying your message so it's super duper important.

I want to install this on our virtual machine so to do that we use the at module and I'm going to structure the task in a bit of a more verbose way. Usually you have a name or you give some description of what you're gonna do like "install Cowsay" and below that you have options. The most important one being the name of the module that you want to execute. Now if you look at the AT module, one of the options is called name which is the name of the package that you actually want to install, so we're gonna pass the name "option" to the At module. So that means we're going to go under at, indent and say "name cowsay" and that's it.

So we try to run our playbook again. The ping works, but the Cowsay does not work. If you check out the error message it says "Could not open lock file. Unable to lock the administration directory 'ru root'" so as you probably know usually when you are in at-git you need to run pseudo at-git so we actually need Ansible to take on pseudo privileges inside of our machine. To do that you're going to add an option to your task. Not on to your module, but to your task called "become". Now "become" is an option to do a number of different things, but most of the time you're just gonna say "become true" What that says is "become the super user so that it can install things."

So the playbook again. Ping runs and this time Cowsay worked. Notice it says "changed" because it dd install Cowsay. We try it again right now it's gonna re-run but this time it's gonna say "Cowsay is okay but not changed". Because remember the modules aren't simply just running commands stupidly. They're making sure the system is in that installed state so Ansible knows when it ran this task wether or not that task actually made a change to your system. This is gonna become an important concept later on.

By the way, one other thing that you'll see with "become" is that instead of "true" a lot of times you'll see the word "yes". In Ansible, whenever you need a Boolean value like true or false, Ansible allows you to say "yes" or "no" so "true" and "yes" are the same; "no" and "false" are the same; makes no difference which one you choose.

So, let's actually start getting our machine setup for real. Get PHP installed, get Nginx installed and get our server rocking.