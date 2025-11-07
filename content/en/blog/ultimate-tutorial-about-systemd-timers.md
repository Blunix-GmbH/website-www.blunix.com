---
title: "The Ultimate Tutorial About Linux Systemd Timers Job Scheduling"
description: "An extensive tutorial on everything there is to know about Linux Systemd Timers, including comprehensible examples and explanations of all configuration options"
date: 2024-04-20
image: "/images/blog/systemd-timer-logo.webp"
image_alt: "The Ultimate Tutorial About Systemd Timers - The Replacement for Cron Jobs"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

This blog post describes how to create, manage and fully understand [systemd](https://systemd.io/) timers, which are a replacement for classic [Cron jobs](https://en.wikipedia.org/wiki/Cron). Systemd timers allow for a much more fine grained configuration of when or upon which events to execute jobs, scripts, commands or systemd services in various Linux distributions.

This article is written for Linux beginners and advanced users. It aims to be easy to understand. While the manual page `man systemd.timer ` can be confusing at times, this tutorial contains extensive practical examples and explains all aspects and configuration options of systemd timers in great detail.

## Table of contents

- [What are Systemd Timers?](#what-are-systemd-timers)
- [Comparison of Systemd Timers versus Cron Jobs](#comparison-of-systemd-timers-versus-cron-jobs)
  - [Advantages to Using Systemd Timers over Cron](#advantages-to-using-systemd-timers-over-cron)
  - [Disadvantages of Using Systemd Timers over Cron](#disadvantages-of-using-systemd-timers-over-cron)
- [Creating a Systemd Timer Example](#creating-a-systemd-timer-example)
  - [Creating a Systemd Timer Configuration File](#creating-a-systemd-timer-configuration-file)
  - [Creating a Systemd Service for the Timer to Execute](#creating-a-systemd-service-for-the-timer-to-execute)
  - [Enabling a Systemd Timer](#enabling-a-systemd-timer)
  - [Showing the Status of a Systemd Timer](#showing-the-status-of-a-systemd-timer)
  - [Disabling a Systemd Timer](#disabling-a-systemd-timer)
  - [Stopping a Systemd Timer](#stopping-a-systemd-timer)
- [Systemd Timers OnCalendar= Syntax with Examples](#systemd-timers-oncalendar-syntax-with-examples)
  - [Which Values Can Be Omitted](#which-values-can-be-omitted)
  - [Every Thursday](#every-thursday)
  - [Specifying Every Possible Value Using an Asterisk](#specifying-every-possible-value-using-an-asterisk)
  - [Ranges between Days](#ranges-between-days)
  - [Lists of Days](#lists-of-days)
  - [Specifying a Different Timezone](#specifying-a-different-timezone)
- [Different Types of Systemd Timers](#different-types-of-systemd-timers)
  - [Real-Time Timers](#real-time-timers)
    - [OnCalendar: Specifying Specific Time Intervals](#oncalendar-specifying-specific-time-intervals)
  - [Monotonic Timers](#monotonic-timers)
    - [OnActiveSec: Since Activation of the Timer](#onactivesec-since-activation-of-the-timer)
    - [OnBootSec: Since the Machine Booted](#onbootsec-since-the-machine-booted)
    - [OnStartupSec: Since Systemd First Started](#onstartupsec-since-systemd-first-started)
    - [OnUnitActiveSec: Since Activation of the Associated Systemd Service](#onunitactivesec-since-activation-of-the-associated-systemd-service)
    - [OnUnitInactiveSec: Since Last Stop of the Associated Systemd Service](#onunitinactivesec-since-last-stop-of-the-associated-systemd-service)
  - [Transient Timers - a replacement for the “at” command](#transient-timers---a-replacement-for-the-at-command)
    - [Transient Systemd Timer 10 Minutes from now](#transient-systemd-timer-10-minutes-from-now)
    - [Transient Systemd Timer to Execute a Command or Script Directly Without Using a Systemd Service](#transient-systemd-timer-to-execute-a-command-or-script-directly-without-using-a-systemd-service)
  - [Triggering Systemd Timers Upon Changes to the System Time](#triggering-systemd-timers-upon-changes-to-the-system-time)
    - [OnClockChange: When the System Clock is Being Changed](#onclockchange-when-the-system-clock-is-being-changed)
    - [OnTimezoneChange: When the System Timezone is Being Changed](#ontimezonechange-when-the-system-timezone-is-being-changed)
- [Configuring Systemd Timers with Randomized Execution Intervals](#configuring-systemd-timers-with-randomized-execution-intervals)
  - [AccuracySec: How Accurate the Systemd Timer Should Obey the Specified Execution Time](#accuracysec-how-accurate-the-systemd-timer-should-obey-the-specified-execution-time)
  - [RandomizedDelaySec: Randomize Execution Time](#randomizeddelaysec-randomize-execution-time)
  - [FixedRandomDelay: Save Randomized Execution Time Across Reboots](#fixedrandomdelay-save-randomized-execution-time-across-reboots)
- [Additional Systemd Timer Configuration Options](#additional-systemd-timer-configuration-options)
  - [Unit: Name of the Associated Systemd Service](#unit-name-of-the-associated-systemd-service)
  - [Persistent: Re-Trigger if the System Was Shut Down During Scheduled Execution](#persistent-re-trigger-if-the-system-was-shut-down-during-scheduled-execution)
  - [WakeSystem: Wake the System From Suspend Mode](#wakesystem-wake-the-system-from-suspend-mode)
  - [RemainAfterElapse: Disable the Timer After Final Execution](#remainafterelapse-disable-the-timer-after-final-execution)
- [Practical Systemd Timer Examples](#practical-systemd-timer-examples)
  - [Systemd Timer Every Minute](#systemd-timer-every-minute)
  - [Systemd Timer Every 5 Minutes](#systemd-timer-every-5-minutes)
  - [Systemd Timer Every Hour or Hourly](#systemd-timer-every-hour-or-hourly)
  - [Systemd Timer Every 2 Hours](#systemd-timer-every-2-hours)
  - [Systemd Timer Every Day or Daily](#systemd-timer-every-day-or-daily)
  - [Systemd Timer Every Day at a Specific Time](#systemd-timer-every-day-at-a-specific-time)
  - [Systemd Timer Every Week or Weekly](#systemd-timer-every-week-or-weekly)
  - [Systemd Timer Every Week at a specific day and time](#systemd-timer-every-week-at-a-specific-day-and-time)
  - [Systemd Timer On Boot](#systemd-timer-on-boot)
  - [Systemd Timer Restart Service](#systemd-timer-restart-service)
  - [Systemd Timer Every 5 Seconds, Every few Seconds or Every Second](#systemd-timer-every-5-seconds-every-few-seconds-or-every-second)
  - [Starting a Systemd Timer Every Few Seconds - The Better Alternative](#starting-a-systemd-timer-every-few-seconds---the-better-alternative)
- [Dependency Management with Systemd Timers](#dependency-management-with-systemd-timers)
  - [Dependencies Between Multiple Systemd Timers](#dependencies-between-multiple-systemd-timers)
- [Debugging Systemd Timers](#debugging-systemd-timers)
  - [Configuring and Using Systemd Timers With Non-Root Users](#configuring-and-using-systemd-timers-with-non-root-users)
  - [Running Systemd Timers as Non-Root Users](#running-systemd-timers-as-non-root-users)
  - [Showing More Iterations With systemd-analyze calendar](#showing-more-iterations-with-systemd-analyze-calendar)
  - [Faking the Time for Testing Systemd Timers](#faking-the-time-for-testing-systemd-timers)
- [Debugging Systemd Timers](#debugging-systemd-timers-1)
  - [Systemd Timers Ignore Syntax Errors in Config Files!](#systemd-timers-ignore-syntax-errors-in-config-files)
  - [List All Systemd Timers](#list-all-systemd-timers)
  - [List All Systemd Timers Including Inactive](#list-all-systemd-timers-including-inactive)
  - [List Specific Systemd Timers Using Wildcards](#list-specific-systemd-timers-using-wildcards)
  - [Viewing Systemt Timers Logs Using Journalctl](#viewing-systemt-timers-logs-using-journalctl)

## [What are Systemd Timers?](#what-are-systemd-timers)

Systemd timers are a scheduling mechanism in Linux systems that use the [systemd init system](https://systemd.io/), like Debian, Ubuntu, Fedora, RedHat, CentOS, openSUSE, Arch Linux, Orcacle Linux, Linux Mint and many others. Systemd timers provide a more modern and flexible alternative to traditional Cron jobs and offer much finer granularity in regard to timing, as well as dependency management.

## [Comparison of Systemd Timers versus Cron Jobs](#comparison-of-systemd-timers-versus-cron-jobs)

Here are the major differences between Systemd Timers and Linux Cron jobs.

### [Advantages to Using Systemd Timers over Cron](#advantages-to-using-systemd-timers-over-cron)

- Cron is limited to minute-level precision, while systemd timers allow you to specify execution using seconds
  [described in Starting a Systemd Timer every few Seconds](#systemd-timer-every-5-seconds-every-few-seconds-or-every-second)

- Systemd timers are integrated with the systemd ecosystem: timers can have dependencies to other systemd timers, services or other units, and can be executed in a specific order

- Systemd timers can be timed according to the actual (wall-clock) time, the time since the system booted, or even be triggered by events, for example like the system booting

- Systemd timers can be started at a random time within a given interval of time (for example at a random minute between 1pm and 2pm)

- When the machine is shut off (or otherwise not operational), systemd will automatically catch up on missed timers once the system is back online (configurable using the
  [Persistent= parameter](#persistent-re-trigger-if-the-system-was-shut-down-during-scheduled-execution)
  )

- Logs are centralized in the systemd journal. Cron jobs used to, by default, generate emails to the Linux user who owns the cronjob when generating output

- Multiple Systemd timers can be set up for multiple specific timezones that are different from the default timezone that is configured for the system

- Systemd timers can be triggered on very uncommon things, like the administrator of a machine
  [manually changing the time](#onclockchange-when-the-system-clock-is-being-changed)

### [Disadvantages of Using Systemd Timers over Cron](#disadvantages-of-using-systemd-timers-over-cron)

- Creating a systemd timer requires FAR more lines of configuration and follow-up commands than configuring a cronjob. If you want to periodically execute a simple BASH command, creating a systemd timer is not enough - systemd timers can only start systemd services, which means you have to configure a systemd service to execute that command, which is then triggered by the systemd timer.

- Some of the configuration options for systemd timers are a bit hard to understand from reading the manual - they are much simpler to understand from this very detailed blogpost ;)

- Systemd timers do not execute at the exact time configured by default, but within +60seconds of that - this however can be configured (or you could say fixed) using
  [AccuracySec=](#accuracysec-how-accurate-the-systemd-timer-should-obey-the-specified-execution-time)

## [Creating a Systemd Timer Example](#creating-a-systemd-timer-example)

The following section describes how to create a systemd timer using the most common usecase for jobs on Linux: periodically executing a simple command or BASH script.

### [Creating a Systemd Timer Configuration File](#creating-a-systemd-timer-configuration-file)

This example shows how to create a very basic and simple systemd timer. First create simple BASH script that writes a string and the current date to `/root/timer-test.log `:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat << 'EOF' > /root/test.sh
#!/bin/bash
echo "I am a test script executing at $(date)" | tee -a /root/test.log
EOF</code></pre>

Make the script executable:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">chmod 700 /root/test.sh</code></pre>

Now create a systemd timer unit for the script: this systemd configuration file is the equivalent to what the execution interval of a cronjob used to be, for example `*/10 * * * * `. The following systemd timer will execute every 10 minutes.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat << EOF > /etc/systemd/system/test.timer
[Unit]
Description=Start the Systemd service test.service every 10 minutes

[Timer]
OnCalendar=*:0/10:*
Persistent=true

[Install]
WantedBy=timers.target
EOF</code></pre>

### [Creating a Systemd Service for the Timer to Execute](#creating-a-systemd-service-for-the-timer-to-execute)

The systemd timer above will execute the systemd service using the same naming scheme - a timer named `/etc/systemd/system/test.timer ` will execute the systemd service `/etc/systemd/system/test.service `. Let’s create a systemd service using the same filename, but with a .service extension:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat << EOF > /etc/systemd/system/test.service
[Unit]
Description=Execute the BASH script /root/test.sh

[Service]
Type=oneshot
ExecStart=/root/test.sh
EOF</code></pre>

After editing the file, you have to execute the following command to make systemd aware of the changes to its configuration. Every time you change a file below `/etc/systemd/system/ `, you have to execute this command.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl daemon-reload</code></pre>

### [Enabling a Systemd Timer](#enabling-a-systemd-timer)

As common with systemd, newly created units, such as timers, have to be explicitly enabled. Note that you run `systemctl start test.timer `, this will execute the timer regardless of what time you configured it to execute!

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl enable test.timer
Created symlink /etc/systemd/system/timers.target.wants/test.timer → /etc/systemd/system/test.timer.</code></pre>

### [Showing the Status of a Systemd Timer](#showing-the-status-of-a-systemd-timer)

To check the status of the new timer:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl status test.timer
● test.timer - Execute the BASH script /root/test.sh every 10 seconds
     Loaded: loaded (/etc/systemd/system/test.timer; enabled; preset: enabled)
     Active: active (waiting) since Tue 2024-04-16 16:19:51 CEST; 48s ago
    Trigger: Tue 2024-04-16 16:20:50 CEST; 9s left
   Triggers: ● test.service</code></pre>

If we now look into the /root/test.log file, it shows that the script is executed every 10 seconds:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat test.log 
I am a test script executing at Tue Apr 16 02:23:50 PM UTC 2024
I am a test script executing at Tue Apr 16 02:24:02 PM UTC 2024
I am a test script executing at Tue Apr 16 02:24:13 PM UTC 2024</code></pre>

If we look into the systemd journal (thats where the logs are) we can see information about the timer being executed:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">journalctl _COMM=systemd
[...]
Apr 16 14:23:50 p1 systemd[1]: Starting test.service - MyScript Service...
Apr 16 14:23:50 p1 systemd[1]: test.service: Deactivated successfully.
Apr 16 14:23:50 p1 systemd[1]: Finished test.service - MyScript Service.
Apr 16 14:24:02 p1 systemd[1]: Starting test.service - MyScript Service...
Apr 16 14:24:02 p1 systemd[1]: test.service: Deactivated successfully.
Apr 16 14:24:02 p1 systemd[1]: Finished test.service - MyScript Service.
Apr 16 14:24:13 p1 systemd[1]: Starting test.service - MyScript Service...
Apr 16 14:24:13 p1 systemd[1]: test.service: Deactivated successfully.
Apr 16 14:24:13 p1 systemd[1]: Finished test.service - MyScript Service.
[...]</code></pre>

Attentive readers might have noticed that we use `tee ` in the test.sh bash script, which means that the script is not only writing the output of the `echo ` command to /root/test.log but also generating output when executed. In order to show the output that was generated by this script, which has been executed by the systemd test.service, use this command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">journalctl _SYSTEMD_UNIT=test.service
Apr 16 14:46:02 p1 test.sh[749373]: I am a test script executing at Tue Apr 16 14:43:31 UTC 2024
Apr 16 14:46:11 p1 test.sh[749420]: I am a test script executing at Tue Apr 16 14:43:31 UTC 2024
Apr 16 14:46:23 p1 test.sh[749467]: I am a test script executing at Tue Apr 16 14:43:31 UTC 2024</code></pre>

### [Disabling a Systemd Timer](#disabling-a-systemd-timer)

To disable a Systemd timer again, use the following commands. Disabling a Systemd timer means preventing it from starting automatically in the future. When you disable a timer, you are essentially removing its association with any target units, making it inactive. However, if the timer is currently running, disabling it does not stop its current execution.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl disable test.timer
Removed "/etc/systemd/system/timers.target.wants/test.timer".</code></pre>

### [Stopping a Systemd Timer](#stopping-a-systemd-timer)

Stopping a systemd timer means halting its current execution if it is running. This action immediately terminates any ongoing execution of the timer. However, stopping a timer does not affect its status as enabled or disabled. Even if you stop a timer, it can still start automatically in the future if it is enabled.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl stop test.timer
# this command does not print output when the timer is successfully stopped</code></pre>

## [Systemd Timers OnCalendar= Syntax with Examples](#systemd-timers-oncalendar-syntax-with-examples)

Lets look at `/etc/systemd/system/test.timer ` and `/etc/systemd/system/test.service ` [that we created above](#creating-a-systemd-service-for-the-timer-to-execute) in detail. To view all available information on systemd timers, refer to `man systemd.timer `.

With systemd timers, OnCalendar is arguably the most relevant configuration option. It allows for very fine grained specification of when to run a specific timer. As with cron, specifying this correctly is a bit confusing at first. The syntax for OnCalendar is:

```systemd
OnCalendar=DayOfWeek Year-Month-Day Hour:Minute:Second
```

To execute a systemd timer one single time at [epoch](<https://en.wikipedia.org/wiki/Epoch_(computing)>) (a common reference point in time in IT), this would be the following. The following timer would execute Thursdays (as in every Thursday) on January first 1970 at 0 hours, 0 minutes and 0 seconds. Note that this statement is exemplary and of course doesn’t make sense in the real world.

```systemd
OnCalendar=Thu 1970-1-1 0:0:0
```

### [Which Values Can Be Omitted](#which-values-can-be-omitted)

As you can see, Thursdays (every Thursday) doesn’t make sense in the constellation above, as we specify a specific date. Hence we can omit the `Thu ` to get the exact same result. If you omit the DayOfWeek, it either means every day of the week, or as in the following example, it means that day of the week that January 1st is:

```systemd
OnCalendar=1970-1-1 0:0:0
```

### [Every Thursday](#every-thursday)

Systemd-timers are usually not intended to start a systemd service one single time, but periodically (you can of course configure them to start only once). To execute a timers assigned systemd service every Thursday, we can specify:

```systemd
OnCalendar=Thu
```

Only specifying `Thu ` means that the timer will be executed at 00:00:00. If you omit values, the tool `systemd-analyze calendar ` is very useful to show you a normalized form of the OnCalendar specification that will actually be used:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemd-analyze calendar "Thu"
  Original form: Thu
Normalized form: Thu *-*-* 00:00:00
    Next elapse: Thu 2024-04-18 00:00:00 UTC
       From now: 1 day 6h left</code></pre>

### [Specifying Every Possible Value Using an Asterisk](#specifying-every-possible-value-using-an-asterisk)

As we are used to from cron, an asterisk (\*) is used to specify all possible values. To have a systemd timer start its systemd service at every 10th minute of every hour (at 0:00:00, 0:10:00, 1:10:00, …, 2:10:00, and so on), this would be:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemd-analyze calendar "*:0/10:*"
  Original form: *:0/10:*
Normalized form: *-*-* *:00/10:*
    Next elapse: Tue 2024-04-16 17:40:00 UTC
       From now: 8min left</code></pre>

### [Ranges between Days](#ranges-between-days)

In order to specify a range, you can use two dots, in the following example that would be every day from Monday to Friday at 8pm:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemd-analyze calendar "Mon..Fri 20:0"
  Original form: Mon..Fri 20:0
Normalized form: Mon..Fri *-*-* 20:00:00
    Next elapse: Tue 2024-04-16 20:00:00 UTC
       From now: 2h 26min left</code></pre>

### [Lists of Days](#lists-of-days)

You can use a comma to specify a list, in this example Mondays, Wednesdays and Fridays at 5am:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemd-analyze calendar "Mon,Wed,Fri 5:0"
  Original form: Mon,Wed,Fri 5:0
Normalized form: Mon,Wed,Fri *-*-* 05:00:00
    Next elapse: Wed 2024-04-17 05:00:00 UTC
       From now: 11h left</code></pre>

### [Specifying a Different Timezone](#specifying-a-different-timezone)

If you run servers in multiple georgiaphical locations, you commonly set your servers timezone to UTC. If you want a systemd timer to run at a specific time in a different timezone, you can specify that too - in this example on saturdays and sundays at 1pm.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemd-analyze calendar "Sat,Sun 13:0 Europe/Berlin"
  Original form: Sat,Sun 13:0 Europe/Berlin
Normalized form: Sat,Sun *-*-* 13:00:00 Europe/Berlin
    Next elapse: Sat 2024-04-20 11:00:00 UTC         
       From now: 3 days left</code></pre>

To list all available timezones:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">timedatectl list-timezones
Africa/Abidjan
Africa/Accra
Africa/Addis_Ababa
[...]</code></pre>

## [Different Types of Systemd Timers](#different-types-of-systemd-timers)

Systemd provide different methods, or categories, of triggering execution of their assigned Systemd services:

- **Real-time**
  : OnCalendar=, triggered by specific time intervals or schedules

- **Monotonic**
  : Triggered by a specific amount of timer elapsing since an event

- **Transient**
  : Timers that only remain active for the current session

- **Changes to the system time**
  : Triggered by changes to the time of the system

### [Real-Time Timers](#real-time-timers)

Real-time timers use the actual real (wall-clock) time to trigger execution.

#### [OnCalendar: Specifying Specific Time Intervals](#oncalendar-specifying-specific-time-intervals)

Real-time timers are configured using OnCalendar=, as described above.

### [Monotonic Timers](#monotonic-timers)

Monotonic timers activate after a designated duration from a specific event, like system boot or unit activation. The supported units are (N means any number)

- 1microsecond or Nmicroseconds

- 1millisecond or Nmilliseconds

- 1second or Nseconds

- 1minute or Nminutes

- 1hour or Nhours

- 1day or Ndays

- 1week or Nweeks

- 1month or Nmonths

- 1year or Nyears

For singular values, like 1year, you can omit the s in yearS. The following options exist for configuring monotonic timers:

#### [OnActiveSec: Since Activation of the Timer](#onactivesec-since-activation-of-the-timer)

Starts a timer after it has been activated, as in `systemctl activate test.timer `:

```systemd
OnActiveSec=3hours 10minutes 17seconds
```

#### [OnBootSec: Since the Machine Booted](#onbootsec-since-the-machine-booted)

Starts a timer relative to the machine booting:

```systemd
OnBootSec=2days 50seconds
```

#### [OnStartupSec: Since Systemd First Started](#onstartupsec-since-systemd-first-started)

Start a timer after systemd itself has been started - this is commonly almost equal to OnBootSec=.

```systemd
OnStartupSec=1minute 10seconds
```

#### [OnUnitActiveSec: Since Activation of the Associated Systemd Service](#onunitactivesec-since-activation-of-the-associated-systemd-service)

Start a timer relative to when the timer was last activated:

```systemd
OnUnitActiveSec=10seconds
```

This means that after running:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl enable test.timer</code></pre>

The timer will execute 10 seconds later.

#### [OnUnitInactiveSec: Since Last Stop of the Associated Systemd Service](#onunitinactivesec-since-last-stop-of-the-associated-systemd-service)

Start a timer relative to when the systemd service it is supposted to start last stopped. If we run a script that executes for 5 seconds, then the following example would execute the timer again 45 seconds later:

```systemd
OnUnitInactiveSec=45seconds
```

### [Transient Timers - a replacement for the “at” command](#transient-timers---a-replacement-for-the-at-command)

Transient timers are only working while your session is open - as in while you are logged in with the current Linux user. If you ssh to a server and start a transient timer, and then log out again, it will not be executed. You can however execute this:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">loginctl enable-linger <username></code></pre>

to leave the users session active in the background after logging out.

To create transient timers the `systemd-run ` command is used.

#### [Transient Systemd Timer 10 Minutes from now](#transient-systemd-timer-10-minutes-from-now)

Execute the systemd service `test.service ` 10 minutes from now:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemd-run --on-active="10minutes" --unit="test.service"</code></pre>

#### [Transient Systemd Timer to Execute a Command or Script Directly Without Using a Systemd Service](#transient-systemd-timer-to-execute-a-command-or-script-directly-without-using-a-systemd-service)

You can also execute a command or script directly. Everything after the command to execute is considered an argument to that command or script:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemd-run --on-active="55minutes" /root/test.sh
systemd-run --on-active="55minutes" /root/test.sh --arg1 --arg2</code></pre>

The tool `systemd-run ` accepts the arguments that resemble the arguments a Systemd timer configuration file accepts, like `--on-active= `, `--on-startup= `, `--on-calendar= `, `--on-clock-change ` and alike. Refer to `man systemd-run ` for additional information.

### [Triggering Systemd Timers Upon Changes to the System Time](#triggering-systemd-timers-upon-changes-to-the-system-time)

The following configuration options can be used to trigger systemd timers upon changes to the clock. I’m not sure how useful this is… But it is part of Systemd Timers.

#### [OnClockChange: When the System Clock is Being Changed](#onclockchange-when-the-system-clock-is-being-changed)

boolean, default: false

From `man systemd.timer `: “When true, the service unit will be triggered when the system clock (CLOCK_REALTIME) jumps relative to the monotonic clock (CLOCK_MONOTONIC)”.

- **CLOCK_REALTIME**
  : the regular clock you most likely see on the bottom / top of your screen in your dock bar, or what you see when you use the command
  `date `
  . You can adjust it manually or by using NTP.

- **CLOCK_MONOTONIC**
  : is started on system boot and counts the time since then. It stops when the system is in suspend mode (suspend to RAM or suspend to disk).

This means that this timer is triggered when a manual update to the system time is made. From the manual page, one might assume that this would also apply to the regular updates that are done via NTP, like by using `ntpdate `, ntpd or systemd-timesyncd, however I was not able to reproduce this. The only way I was able to trigger this was by manually force-setting the time, as shown in the following example:

A Systemd timer using `OnClockChange=true ` as trigger:

```systemd
[Unit]
Description=Execute the BASH script /root/test.sh

[Timer]
OnClockChange=true

[Install]
WantedBy=timers.target
```

The systemd service that this timer triggers:

```systemd
[Unit]
Description=MyScript Service

[Service]
Type=oneshot
ExecStart=/root/test.sh
```

The BASH script the service executes:

```bash
#!/bin/bash

echo "I am a test script executing at $(date)" | tee -a /root/test.log
```

Disabling NTP for the system:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">timedatectl set-ntp false</code></pre>

Manually setting the time:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">date -s '2019-10-17 12:00:00'
Do 17. Okt 12:00:00 UTC 2019</code></pre>

The following result shows that the timer was triggered, which started the service, which executed the BASH script:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat /root/test.log
I am a test script executing at Do 17. Okt 12:00:00 UTC 2019</code></pre>

Re-enabling NTP:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">timedatectl set-ntp true</code></pre>

Switching back to NTP managed time also triggered the Systemd timer:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat /root/test.log
I am a test script executing at Do 17. Okt 12:00:00 UTC 2019
I am a test script executing at Mi 17. Apr 21:02:13 UTC 2024</code></pre>

#### [OnTimezoneChange: When the System Timezone is Being Changed](#ontimezonechange-when-the-system-timezone-is-being-changed)

boolean, default: false

Executes the timer when you set a new timezone:

```systemd
OnTimezoneChange=true
```

Change the timezone using the following command to trigger the timer:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">timedatectl set-timezone America/New_York</code></pre>

## [Configuring Systemd Timers with Randomized Execution Intervals](#configuring-systemd-timers-with-randomized-execution-intervals)

The following configuration options can be used to execute systemd timers at random times within a definable timeframe.

### [AccuracySec: How Accurate the Systemd Timer Should Obey the Specified Execution Time](#accuracysec-how-accurate-the-systemd-timer-should-obey-the-specified-execution-time)

default: 1min

This configuration option is very important to understand if you want to execute a timer at exactly a given time, down to the second.

**By default, systemd timers do NOT execute EXACTLY at the time you specified, but within a variance of AccuracySec=, which by default is one minute!**

This means that if you set a timer to execute OnCalendar=21:00:00, it might not execute exactly at 21:00:00 but anywhere between 21:00:00 and 21:01:00, unless you adjusted the AccuracySec= setting, which defaults to one minute.

Here is an example: a systemd timer set to execute every 15 seconds:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat /etc/systemd/system/test.timer
[Unit]
Description=Execute the BASH script /root/test.sh every 15 seconds

[Timer]
OnCalendar=*:*:0/15

[Install]
WantedBy=timers.target</code></pre>

When looking at the logs, we can see that the script is not executed exactly every 15 seconds, but has quite a few additional seconds of variance for each execution:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">journalctl -n 0 -f _SYSTEMD_UNIT=test.service
Apr 17 22:02:16 host test.sh[3536562]: I am a test script executing at Mi 17. Apr 22:02:16 UTC 2024
Apr 17 22:02:31 host test.sh[3536572]: I am a test script executing at Mi 17. Apr 22:02:31 UTC 2024
Apr 17 22:02:48 host test.sh[3536576]: I am a test script executing at Mi 17. Apr 22:02:48 UTC 2024
Apr 17 22:03:05 host test.sh[3536657]: I am a test script executing at Mi 17. Apr 22:03:05 UTC 2024
Apr 17 22:03:18 host test.sh[3536695]: I am a test script executing at Mi 17. Apr 22:03:18 UTC 2024</code></pre>

You can “fix this” using `AccuracySec=1s `:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat /etc/systemd/system/test.timer
[Unit]
Description=Execute the BASH script /root/test.sh

[Timer]
OnCalendar=*:*:0/15
AccuracySec=1

[Install]
WantedBy=timers.target</code></pre>

As you can see in the following results, the execution time is perfectly accurate now:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">journalctl -n 0 -f _SYSTEMD_UNIT=test.service
Apr 17 22:00:00 host test.sh[3536066]: I am a test script executing at Mi 17. Apr 22:00:00 UTC 2024
Apr 17 22:00:15 host test.sh[3536136]: I am a test script executing at Mi 17. Apr 22:00:15 UTC 2024
Apr 17 22:00:30 host test.sh[3536142]: I am a test script executing at Mi 17. Apr 22:00:30 UTC 2024
Apr 17 22:00:45 host test.sh[3536168]: I am a test script executing at Mi 17. Apr 22:00:45 UTC 2024
Apr 17 22:01:00 host test.sh[3536270]: I am a test script executing at Mi 17. Apr 22:01:00 UTC 2024</code></pre>

The manual page `man systemd.timer ` states: “To get the best accuracy, set this option to 1us” - I was not able to get better results by settings this to anything lower than 1 second. The reason for this behavior, or for the default of one minute, is, according to the manual page: “in order to optimize power consumption to suppress unnecessary CPU wake-ups”.

If you are migrating from cron this behavior will most likely be very confusing (and remain undetected) for most users. As you most likely run recurring jobs on a server (where you don’t care about the marginally tiny additional power of systemd timers next to your red-hot-glowing database or PHP application), I would recommend to define this setting in all systemd timers you configure and set it to 1 second.

### [RandomizedDelaySec: Randomize Execution Time](#randomizeddelaysec-randomize-execution-time)

If you have to run programs on multiple machines in your cluster, but you don’t want to run them all at the same time, this option is for you. It will simply randomize the execution of the timer within the given timeframe. The following example will execute the timer every day at a random time between 00:00:00 and 00:10:00.

Note that the Sec in RandomizedDelaySec indeed does stand for seconds - if you specify `RandomizedDelaySec=30 ` it means 30 seconds. You can also specify `RandomizedDelaySec=30seconds `, which is equal to just specifying 30, or you can specify `RandomizedDelaySec=5minutes ` or alike.

```systemd
OnCalendar=0:0:0
RandomizedDelaySec=10minutes
```

To clarify the `0:0:0 `:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime '1970-01-01 00:00:00' systemd-analyze calendar "0:0:0"
  Original form: 0:0:0
Normalized form: *-*-* 00:00:00
    Next elapse: Fri 1970-01-02 00:00:00 UTC
       From now: 23h left</code></pre>

Note that in this constellation, the value of [AccuracySec=, which is described above](#accuracysec-how-accurate-the-systemd-timer-should-obey-the-specified-execution-time), might also be important. AccuracySec= defaults to one minute. If you define RandomizedDelaySec=5minutes for a systemd timer that is supposed to execute daily at 00:00:00, it will execute between 00:00:00 and 00:06:00. To prevent this, set AccuracySec=1 (one second). The manual (often) recommends to set AccuracySec=1us (one millisecond), which I think does not make sense and acchieves the exact opposite of what AccuracySec= was designed to do - prevent unneccessary CPU wakeups. Refer to [Starting a Systemd Timer Every Few Seconds - The Better Alternative](#starting-a-systemd-timer-every-few-seconds---the-better-alternative) for information on how to best execute tasks more often than once a minute.

### [FixedRandomDelay: Save Randomized Execution Time Across Reboots](#fixedrandomdelay-save-randomized-execution-time-across-reboots)

boolean, default: false

This boolean configuration option specifies if the randomization time is fixed across reboots. If you set this to true, the random interval will always be identical. Settings RandomizedDelaySec=0 disables this. The default is false.

The exact random time that systemd chooses to execute your timer can obviously not be predicted in this blog post. From `man systemd.timer `: “The offset depends on the machine ID, user identifier and timer name”. Hence the following example is only meant for better understanding - we may assume that the following example will ALWAYS execute the timer at 00:07:31, even after a reboot.

```systemd
OnCalendar=daily
RandomizedDelaySec=10minutes
FixedRandomDelay=true
```

## [Additional Systemd Timer Configuration Options](#additional-systemd-timer-configuration-options)

### [Unit: Name of the Associated Systemd Service](#unit-name-of-the-associated-systemd-service)

By default a timer `/etc/systemd/systemd/test.timer ` would activate a systemd service `/etc/systemd/system/test.service `. To override this behavior, you can specify the Unit= like so:

```systemd
Unit=my_test
```

Which would activate `/etc/systemd/system/my_test.service `. I do not recommend deviating from the default by using this option, as it just makes your configuration below `/etc/systemd/system/ ` more confusing.

### [Persistent: Re-Trigger if the System Was Shut Down During Scheduled Execution](#persistent-re-trigger-if-the-system-was-shut-down-during-scheduled-execution)

boolean, default: false

This boolean configuration option only applies to timers configured with OnCalendar=. If a timer was due to trigger during system downtime, it will execute once the system is back online. When set to false, the timer will not be executed once the machine starts. If it missed several executions while the system was shut off or was otherwise not operational, it will only run once.

Setting this to true will save the last execution time to disk. This file is stored in `/var/lib/systemd/timers/stamp-test.timer ` for a systemd timer file named `/etc/systemd/system/test.timer `. The timestamp is not actually stored in the file, but the files modify time will simply be updated. Here are the timestamps for such a file for a Systemd timer with Persistent=true that is configured to execute once every second:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">while true; do ls -la --time-style=full-iso /var/lib/systemd/timers/stamp-test.timer; sleep 1; done
-rw-r--r-- 1 root root 0 2024-04-18 00:44:36.096114000 +0000 /var/lib/systemd/timers/stamp-test.timer
-rw-r--r-- 1 root root 0 2024-04-18 00:44:37.831349000 +0000 /var/lib/systemd/timers/stamp-test.timer
-rw-r--r-- 1 root root 0 2024-04-18 00:44:38.831427000 +0000 /var/lib/systemd/timers/stamp-test.timer
-rw-r--r-- 1 root root 0 2024-04-18 00:44:39.831595000 +0000 /var/lib/systemd/timers/stamp-test.timer</code></pre>

You can use `systemctl clean --what=state test.timer ` to delete this timestamp file. The manual `man systemd.timer ` recommends that you execute this command before uninstalling a timer that was configured with `Persistent=true `. If you only stop and disable a timer, this file will not be removed.

### [WakeSystem: Wake the System From Suspend Mode](#wakesystem-wake-the-system-from-suspend-mode)

boolean, default: false

This configuration option is less for servers but more for workstations: if your workstation is in sleeping (suspend to disk or suspend to RAM), setting this option to true will cause your machine to wake up and execute the timer. (Note: I have not tested this and I assume this will need some debugging). Also note that it will not put it back into suspend after executing the timer. Additionally note that this only works if the systemd timer is configured as the root user.

If **false**, the timer uses a monotonic clock named “CLOCK_MONOTONIC”, which is paused during a suspended system. CLOCK_MONOTONIC is started on system boot and counts the time since then. Refer to `man clock_gettime ` and search for “CLOCK_MONOTONIC” for additional information.

If **true**, the timer uses the monotonic clock “CLOCK_BOOTTIME”, which is similar to CLOCK_MONOTONIC, except that it continues when the system is suspended. Because of this, systemd timers can be triggered even if the system is suspended. Refer to `man clock_getres ` and search for “CLOCK_BOOTTIME” for additional information.

### [RemainAfterElapse: Disable the Timer After Final Execution](#remainafterelapse-disable-the-timer-after-final-execution)

boolean, default: true

Systemd timers often speak of “elapsed timers” - this means that the time until the systemd timer is triggered has “elapsed” - meaning that it is now time to activate the systemd service that the systemd timer is configured for.

When set to false, this configuration option will deactivate a systemd timer after its final configured execution.

The difference between an active and an inactive timer is this:

<pre class="command-line language-bash" data-output="2-3,5,7" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl status test.timer | grep Active
     Active: active (waiting) since Wed 2024-04-17 20:32:59 UTC; 8s ago

systemctl stop test.timer 

systemctl status test.timer | grep Active
     Active: inactive (dead)</code></pre>

In the following example, I used an OnCalendar value that will only trigger the timer a finite amount of times. I tried this with `OnCalendar=*:0/1:0 ` (once per minute) and it did not work - the timer stayed active and kept executing. Hence this option is useful for deactivating a timer that will trigger a limited amount of times and then never again, and after all possible trigger times are over, it will automatically deactivate the systemd timer.

Here is the example systemd timer - it executes a systemd service that runs the BASH script /root/test.sh every 10 seconds from 21:30:00 until 21:30:50.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat /etc/systemd/system/test.timer
[Unit]
Description=Execute the BASH script /root/test.sh

[Timer]
OnCalendar=2024-04-17 21:30:0/10
RemainAfterElapse=false

[Install]
WantedBy=timers.target</code></pre>

First time showing the timers status at 21:29:53 - it says it will execute in 7 seconds:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl status test.timer 

● test.timer - Execute the BASH script /root/test.sh
     Loaded: loaded (/etc/systemd/system/test.timer; enabled; preset: enabled)
     Active: active (waiting) since Wed 2024-04-17 21:29:50 UTC; 1s ago
      Until: Wed 2024-04-17 21:29:50 UTC; 1s ago
    Trigger: Wed 2024-04-17 21:30:00 UTC; 7s left
   Triggers: ● test.service</code></pre>

I now waited one minute and the timer executed every 10 seconds from 21:30:00 to 21:30:50. After that, it stopped executing as defined in its OnCalendar setting, and also deactivated itself at 21:30:56:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl status test.timer 
○ test.timer - Execute the BASH script /root/test.sh
     Loaded: loaded (/etc/systemd/system/test.timer; enabled; preset: enabled)
     Active: inactive (dead) since Wed 2024-04-17 21:30:56 UTC; 14s ago
   Duration: 1min 6.515s
    Trigger: n/a
   Triggers: ● test.service

Apr 17 21:29:50 host systemd[1]: Started test.timer - Execute the BASH script /root/test.sh.
Apr 17 21:30:56 host systemd[1]: test.timer: Deactivated successfully.</code></pre>

## [Practical Systemd Timer Examples](#practical-systemd-timer-examples)

Here are some practical examples for scheduling systemd timers. Please refer to [the description above](#basic-systemd-timer-examples) on how to create the systemd timer configuration files for them.

The following section provides examples for the OnCalendar= configuration option. Please refer to the [detailed description of how to use OnCalendar=](#systemd-timers-oncalendar-syntax-with-examples) for all information regarding its syntax.

To syntax-check your own configurations of OnCalendar=, you can use the tool `systemd-analyze calendar `. All following examples use the command `faketime '1970-01-01 00:00:00' ` in front of the `systemd-analyze calendar ` command in order to improve readability when identifying the remaining time for the systemd timer to execute.

### [Systemd Timer Every Minute](#systemd-timer-every-minute)

If you want to use Systemd timers that execute precicely at a specific second, like at 05:45:00am, also [read this section](#systemd-timer-every-5-seconds-every-few-seconds-or-every-second) on how to disable +60 seconds of random time added to each timer by default, and how to disable this!

Example:

```systemd
OnCalendar="*:0/1:0"
```

Verification:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime '1970-01-01 00:00:00' systemd-analyze calendar "*:0/1:0"
  Original form: *:0/1:0
Normalized form: *-*-* *:00/1:00
    Next elapse: Thu 1970-01-01 00:01:00 UTC
       From now: 59s left</code></pre>

### [Systemd Timer Every 5 Minutes](#systemd-timer-every-5-minutes)

If you want to use Systemd timers that execute precicely at a specific second, like at 05:45:00am, also [read this section](#systemd-timer-every-5-seconds-every-few-seconds-or-every-second) on how to disable +60 seconds of random time added to each timer by default, and how to disable this!

Example:

```systemd
OnCalendar="*:0/5:0"
```

Verification:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime '1970-01-01 00:00:00' systemd-analyze calendar "*:0/5:0"
  Original form: *:0/5:0
Normalized form: *-*-* *:00/5:00
    Next elapse: Thu 1970-01-01 00:05:00 UTC
       From now: 4min 59s left</code></pre>

### [Systemd Timer Every Hour or Hourly](#systemd-timer-every-hour-or-hourly)

Example:

```systemd
OnCalendar="hourly"
```

Verification:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime '1970-01-01 00:00:00' systemd-analyze calendar "hourly"
  Original form: hourly
Normalized form: *-*-* *:00:00
    Next elapse: Thu 1970-01-01 01:00:00 UTC
       From now: 59min left</code></pre>

### [Systemd Timer Every 2 Hours](#systemd-timer-every-2-hours)

Example:

```systemd
OnCalendar="0/2:0:0"
```

Verification:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime '1970-01-01 00:00:00' systemd-analyze calendar "0/2:0:0"
  Original form: 0/2:0:0
Normalized form: *-*-* 00/2:00:00
    Next elapse: Thu 1970-01-01 02:00:00 UTC
       From now: 1h 59min left</code></pre>

### [Systemd Timer Every Day or Daily](#systemd-timer-every-day-or-daily)

Example:

```systemd
OnCalendar="daily"
```

Verification:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime '1970-01-01 00:00:00' systemd-analyze calendar "daily"
  Original form: Mon..Sun
Normalized form: *-*-* 00:00:00
    Next elapse: Fri 1970-01-02 00:00:00 UTC
       From now: 23h left</code></pre>

### [Systemd Timer Every Day at a Specific Time](#systemd-timer-every-day-at-a-specific-time)

Example:

```systemd
OnCalendar="Mon..Sun 14:30"
```

Verification:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime '1970-01-01 00:00:00' systemd-analyze calendar "Mon..Sun 14:30"
  Original form: Mon..Sun 14:30
Normalized form: *-*-* 14:30:00
    Next elapse: Thu 1970-01-01 14:30:00 UTC
       From now: 14h left</code></pre>

### [Systemd Timer Every Week or Weekly](#systemd-timer-every-week-or-weekly)

Specifying “weekly” will execute the timer each Monday at 00:00:00. Example:

```systemd
OnCalendar="weekly"
```

Verification:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime '1970-01-01 00:00:00' systemd-analyze calendar "weekly"
  Original form: weekly
Normalized form: Mon *-*-* 00:00:00
    Next elapse: Mon 1970-01-05 00:00:00 UTC
       From now: 3 days left</code></pre>

### [Systemd Timer Every Week at a specific day and time](#systemd-timer-every-week-at-a-specific-day-and-time)

The following example might be more practical, which will execute the timer every tuesday at 8am:

```systemd
OnCalendar="Tue 8:00"
```

Verification:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime '1970-01-01 00:00:00' systemd-analyze calendar "Tue 8:00"
  Original form: Tue 8:00
Normalized form: Tue *-*-* 08:00:00
    Next elapse: Tue 1970-01-06 08:00:00 UTC
       From now: 5 days left</code></pre>

### [Systemd Timer On Boot](#systemd-timer-on-boot)

The best way to start a systemd timer after booting the machine is to use the `OnStartupSec= ` configration option, which will start the timer within the specified timeframe after systemd itself was first started:

```systemd
OnBootSec="10 seconds"
```

### [Systemd Timer Restart Service](#systemd-timer-restart-service)

This example describes how to periodically restart a systemd service using a systemd timer.

Systemd timers themselves (the .timer files) are only responsible for timing to START systemd services - when a timer reaches its configured time to execute, it will run a `systemctl start some.service `.

However you can simply create a oneshot services that executes a systemctl command, like `systemctl restart php8.4-fpm.service `. Starting this particular service would then restart another service.

In order to restart a systemd service using a systemd timer, we have to create both a .timer and a .service file - where the .service file contains a single systemctl command to restart the actual service we want to restart.

Here is a complete example that restarts the systemd service php8.4-fpm using a systemd timer every two hours.

Save the following into `/etc/systemd/system/restart-php-fpm.timer `:

```systemd
[Unit]
Description=Trigger the systemd service restart-php-fpm.service every two hours

[Timer]
OnCalendar=*-*-* 0/2:00:00

[Install]
WantedBy=timers.target
```

And create a systemd service of type OneShot to actually execute the `systemctl restart php8.4-fpm.service ` command. Save the following content to `/etc/systemd/system/restart-php-fpm.service `:

```systemd
[Unit]
Description=Restart the systemd service php8.4-fpm.service

[Service]
Type=oneshot
ExecStart=/bin/systemctl restart php8.4-fpm.service
```

To apply the changes to the systemd configuration files below `/etc/systemd/system/ `:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl daemon-reload</code></pre>

To enable the timer:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl enable restart-php-fpm.timer
Created symlink /etc/systemd/system/timers.target.wants/restart-php-fpm.timer → /etc/systemd/system/restart-php-fpm.timer.</code></pre>

To show the status of the timer:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl status restart-php-fpm.timer
● restart-php-fpm.timer - Trigger the systemd service restart-php-fpm.service every two hours
     Loaded: loaded (/etc/systemd/system/restart-php-fpm.timer; enabled; preset: enabled)
     Active: active (waiting) since Tue 2024-04-16 22:00:01 UTC; 4ms ago
      Until: Tue 2024-04-16 22:00:01 UTC; 4ms ago
    Trigger: Wed 2024-04-17 00:00:00 UTC; 1h 59min left
   Triggers: ● restart-php-fpm.service</code></pre>

If you want to test the timer, you can “start” it - note that “enable” means to enable the timer, as in make it wait for its execution time, and “start” means to actually run the timer, regardless of what you configured for its execution time using OnCalendar=:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl start restart-php-fpm.timer
# This command does not print output on successful execution</code></pre>

Refer to the [debugging section of this blog post](#debugging-systemd-timers) for information on how to check the `journalctl ` logs if the systemd timer executed successfully.

### [Systemd Timer Every 5 Seconds, Every few Seconds or Every Second](#systemd-timer-every-5-seconds-every-few-seconds-or-every-second)

As [described in the section below on how to do this better](#starting-a-systemd-timer-every-few-seconds---the-better-alternative), I do not consider using systemd timers to execute a systemd service more often than once a minute to be a good approach. It however is possible using Systemd timers, and here is a complete description on how to do this correctly and accurately to the second.

When running systemd timers in a second interval, there are several things to consider. First of, as described in [the AccuracySec= section](#accuracysec-how-accurate-the-systemd-timer-should-obey-the-specified-execution-time), systemd timers do not obey the time you specify, for example using OnCalendar=, down to the second, unless you explicity configure it to do so. The setting AccuracySec= has a default value of one minute, meaning that systemd will execute the timer with a plus one minute variance - if you set a timer for 00:00:00, it will execute between 00:00:00 and 00:01:00.

Down to executing a timer every 15 seconds, this behavior can be “fixed” by setting `AccuracySec=1second `. A systemd timer like `OnCalendar=*:*:0/15 ` will then reliably execute every 15 seconds, down to the second. When specifying an execution interval of less than 15 seconds, I have experienced further difficulties while writing this blog post (keep reading for the solution).

The first problem I encountered is that systemd will start to complain if you restart a systemd service to often - as systemd timers simply trigger systemd services, this applies and triggers the following error message. Let’s configure OnCalendar to execute every 5 seconds:

```systemd
OnCalendar="*:*:0/5"
```

Resulting error message:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">root@host:~# journalctl -f --identifier systemd
Apr 18 00:09:46 host systemd[1]: test.service: Start request repeated too quickly.
Apr 18 00:09:46 host systemd[1]: test.service: Failed with result 'start-limit-hit'.
Apr 18 00:09:46 host systemd[1]: Failed to start test.service - MyScript Service.</code></pre>

According to [this stackoverflow post](https://serverfault.com/questions/845471/service-start-request-repeated-too-quickly-refusing-to-start-limit) the “default limit is to allow 5 restarts in a 10sec period. If a service goes over that threshold due to the Restart= config option in the service definition, it will not attempt to restart any further.” With starting a service every 5 seconds, we should be below that however. Regardless of this, here is the solution to how to use a systemd timer once a second. From `man systemd.unit `:

```plaintext
StartLimitIntervalSec=interval, StartLimitBurst=burst

Configure unit start rate limiting. Units which are started more than burst times within an interval time span are not permitted to start any more.
Use StartLimitIntervalSec= to configure the checking interval and StartLimitBurst= to configure how many starts per interval are allowed.
```

When configuring the systemd service that we want to start once a second using the the timer like this:

```systemd
[Unit]
Description=MyScript Service

[Service]
Type=oneshot
ExecStart=/root/test.sh
StartLimitIntervalSec=1
StartLimitBurst=10
```

I was able to reliably trigger the systemd timer once a second. Here is the timer file I used:

```systemd
[Unit]
Description=Execute the BASH script /root/test.sh

[Timer]
OnCalendar=*:*:0/1
AccuracySec=1

[Install]
WantedBy=timers.target
```

Here is the result:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">root@host:~# journalctl -f --identifier test.sh
Apr 18 00:32:55 host test.sh[3570468]: I am a test script executing at Do 18. Apr 00:32:55 UTC 2024
Apr 18 00:32:56 host test.sh[3570472]: I am a test script executing at Do 18. Apr 00:32:56 UTC 2024
Apr 18 00:32:57 host test.sh[3570476]: I am a test script executing at Do 18. Apr 00:32:57 UTC 2024
Apr 18 00:32:58 host test.sh[3570480]: I am a test script executing at Do 18. Apr 00:32:58 UTC 2024
Apr 18 00:32:59 host test.sh[3570485]: I am a test script executing at Do 18. Apr 00:32:59 UTC 2024</code></pre>

As you can see the Systemd timer now reliably executes its assigned service once a second without any errors.

### [Starting a Systemd Timer Every Few Seconds - The Better Alternative](#starting-a-systemd-timer-every-few-seconds---the-better-alternative)

Using systemd timers to execute a systemd service every few seconds is, in my opinion, not a good approach - what you most likely want to do is to write is a program or daemon, which executes your task at the desired interval of seconds. The simplest example in a BASH script would be:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat << EOF > /usr/local/bin/my-daemon.sh
#!/bin/bash

while true; do
    echo "Do things"
    sleep 5
done
EOF</code></pre>

Make the script executable:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">chmod 700 /usr/local/bin/my-daemon.sh</code></pre>

You can set up a systemd service for this like so:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat << EOF > /etc/systemd/system/my-daemon.service
[Unit]
Description=My Daemon Service
After=syslog.target

[Service]
Type=simple
ExecStart=/usr/local/bin/my-daemon.sh
Restart=always
SyslogIdentifier=my-daemon

[Install]
WantedBy=multi-user.target
EOF</code></pre>

Reload the systemd daemon to apply the changes:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl daemon-reload</code></pre>

And start the service:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl start my-daemon.service</code></pre>

This uses `SyslogIdentifier=my-daemon ` in the systemd service to set a unique syslog identifier, which we can now use to filter the logs for only this service with journalctl:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">journalctl -f --identifier my-daemon
Apr 18 00:00:00 host my-daemon[3564012]: Do things
Apr 18 00:00:05 host my-daemon[3564012]: Do things
Apr 18 00:00:10 host my-daemon[3564012]: Do things
Apr 18 00:00:15 host my-daemon[3564012]: Do things
Apr 18 00:00:20 host my-daemon[3564012]: Do things</code></pre>

## [Dependency Management with Systemd Timers](#dependency-management-with-systemd-timers)

Systemd allows you to define dependencies to other Systemd timers or Systemd services using the configuration Options `Requires= `, `After= ` and `Wants= `:

**Requires=**

- The Requires= directive specifies other units that are required by the current unit

- If the units specified in Requires= are not active, systemd will attempt to activate them when the current unit is started

- If any of the required units fail to activate or are not available, the current unit will fail to start as well

- The Requires= directive creates a strong dependency between units, ensuring that they are started together and stopped together

**After=**

- The After= directive specifies other units that should be started before the current unit

- It does not imply a dependency relationship like Requires=. Instead, it only specifies the order in which units should be started

- Units listed in After= may or may not be activated concurrently with the current unit.

- Use After= to control the sequencing of unit activation to ensure that certain units are started before others but without creating a strict dependency

**Wants=**

- The Wants= directive specifies other units that the current unit wants to be started with but does not strictly require

- Unlike Requires=, failure to start units listed in Wants= will not prevent the current unit from starting

- Units listed in Wants= are started if possible, but failure to start them does not affect the current unit

- Wants= creates a weaker dependency compared to Requires=, which allows units to start independently but together if possible

### [Dependencies Between Multiple Systemd Timers](#dependencies-between-multiple-systemd-timers)

Here is an example with two systemd timer units: test1.timer and test2.timer. We want test2.timer to only start after test1.timer has finished successfully.

The first timer `/etc/systemd/system/timer1.timer ` is just a simpler timer, configured to start daily at 00:00:00:

```systemd
# timer1.timer
[Unit]
Description=Timer 1

[Timer]
OnCalendar=0:0:0
Persistent=true

[Install]
WantedBy=timers.target
```

The second timer is set up to start at 01:00:00. However it also uses `Requires=timer1.timer ` and `After=timer1.timer `.

```systemd
# timer2.timer
[Unit]
Description=Timer 2
Requires=timer1.timer
After=timer1.timer

[Timer]
OnCalendar=1:0:0
Persistent=true

[Install]
WantedBy=timers.target
```

This means that:

- If timer1 successfully finishes executing its assigned systemd service at 01:15:00, timer2 will start at 01:15:00 and not at 01:00:00

- If timer1’s assigned systemd service fails during its execution, timer2 will not execute

## [Debugging Systemd Timers](#debugging-systemd-timers)

Here are some informations that will be helpful in debugging Systemd timers.

### [Configuring and Using Systemd Timers With Non-Root Users](#configuring-and-using-systemd-timers-with-non-root-users)

Systemd timer units can not only be configured as the Linux root user - unprivileged Linux users can use those as well. Here is how to create an example Systemd timer as regular Linux user.

First create a config directory:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">mkdir -p ~/.config/systemd/user/</code></pre>

Now create a timer unit configuration file:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat << EOF > ~/.config/systemd/user/test.timer
[Unit]
Description=Start the non-root user Systemd service test.service every 10 minutes

[Timer]
OnCalendar=*:0/10:*
Persistent=true

[Install]
WantedBy=timers.target
EOF</code></pre>

Now create a Systemd service file:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">cat << EOF > ~/.config/systemd/user/test.service
[Unit]
Description=Execute the BASH script /root/test.sh

[Service]
Type=oneshot
ExecStart=/bin/echo teststring
EOF</code></pre>

To activate the timer first reload systemd to accept the new configuration:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl --user daemon-reload</code></pre>

And then enable the timer:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl --user enable test.timer</code></pre>

Important: User timers only run during an active session! To keep Systemd timers running after logging out, use this:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">sudo loginctl enable-linger <user></code></pre>

### [Running Systemd Timers as Non-Root Users](#running-systemd-timers-as-non-root-users)

As Systemd timers only start Systemd services, you can simply configure the systemd service that the timer will start to run as a specific user and group:

```systemd
[Unit]
Description=Your Service

[Service]
Type=simple
ExecStart=/path/to/your/command
User=your_username
Group=your_groupname

[Install]
WantedBy=multi-user.target
```

### [Showing More Iterations With systemd-analyze calendar](#showing-more-iterations-with-systemd-analyze-calendar)

To show more iterations in `systemd-analyze calendar `, use `--iterations=N `:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemd-analyze calendar "Sat,Sun 13:0 Europe/Berlin" --iterations=4
  Original form: Sat,Sun 13:0 Europe/Berlin
Normalized form: Sat,Sun *-*-* 13:00:00 Europe/Berlin
    Next elapse: Sat 2024-04-20 11:00:00 UTC         
       From now: 3 days left
       Iter. #2: Sun 2024-04-21 11:00:00 UTC         
       From now: 4 days left
       Iter. #3: Sat 2024-04-27 11:00:00 UTC         
       From now: 1 week 3 days left
       Iter. #4: Sun 2024-04-28 11:00:00 UTC         
       From now: 1 week 4 days left</code></pre>

### [Faking the Time for Testing Systemd Timers](#faking-the-time-for-testing-systemd-timers)

To improve readability with `systemd-analyze calendar `, you can use `faketime ` to fake a specific time for all following commands:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">faketime 'last Friday 5 pm' systemd-analyze calendar "Sat,Sun 13:0 Europe/Berlin"
  Original form: Sat,Sun 13:0 Europe/Berlin
Normalized form: Sat,Sun *-*-* 13:00:00 Europe/Berlin
    Next elapse: Sat 2024-04-13 11:00:00 UTC         
       From now: 17h left</code></pre>

See `man faketime ` for additional information.

## [Debugging Systemd Timers](#debugging-systemd-timers-1)

Here is some information that might help you to debug your Systemd timers.

### [Systemd Timers Ignore Syntax Errors in Config Files!](#systemd-timers-ignore-syntax-errors-in-config-files)

When activating a timer that contains non-critical errors, systemd silently disregards them! For instance:

```systemd
[Unit]
Description=Start the Systemd service test.service every 10 minutes

[Timer]
OnClendar=*:0/10:*    <== NOTE THE MISSING a IN Clendar
Persistent=true

[Install]
WantedBy=timers.target
```

Make sure to verify your Systemd unit files with this command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemd-analyze verify  /etc/systemd/system/test.timer 
/etc/systemd/system/test.timer:5: Unknown key name 'OnClendar' in section 'Timer', ignoring.
test.timer: Timer unit lacks value setting. Refusing.
Unit test.timer has a bad unit file setting.</code></pre>

Example `journalctl ` logs:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">journalctl -f
Apr 20 00:54:42 host systemd[1]: /etc/systemd/system/test.timer:5: Unknown key name 'OnClendar' in section 'Timer', ignoring.
Apr 20 00:54:42 host systemd[1]: test.timer: Timer unit lacks value setting. Refusing.</code></pre>

### [List All Systemd Timers](#list-all-systemd-timers)

To list all configured and active Systemd timers:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl list-timers
NEXT                        LEFT           LAST                        PASSED        UNIT                           ACTIVATES                       
Sat 2024-04-20 02:51:31 UTC 2h 2min left   Fri 2024-04-19 11:48:43 UTC 13h ago       plocate-updatedb.timer         plocate-updatedb.service
Sat 2024-04-20 06:59:18 UTC 6h left        Fri 2024-04-19 11:48:43 UTC 13h ago       apt-daily-upgrade.timer        apt-daily-upgrade.service
Sat 2024-04-20 07:33:29 UTC 6h left        Sat 2024-04-20 00:24:10 UTC 25min ago     anacron.timer                  anacron.service
[...]</code></pre>

### [List All Systemd Timers Including Inactive](#list-all-systemd-timers-including-inactive)

To also list inactive timers:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl list-timers --all
NEXT        LEFT     LAST     PASSED    UNIT                           ACTIVATES                       
[...]
-           -        -        -         apport-autoreport.timer        apport-autoreport.service
-           -        -        -         restart-php-fpm.timer          
-           -        -        -         snapd.snap-repair.timer        snapd.snap-repair.service</code></pre>

### [List Specific Systemd Timers Using Wildcards](#list-specific-systemd-timers-using-wildcards)

To list all Systemd timers matching a specific wildcard:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">systemctl list-timers apt*
NEXT                        LEFT    LAST                        PASSED       UNIT                    ACTIVATES
Sat 2024-04-20 06:59:18 UTC 6h left Fri 2024-04-19 11:48:43 UTC 13h ago      apt-daily-upgrade.timer apt-daily-upgrade.service
Sat 2024-04-20 10:20:51 UTC 9h left Fri 2024-04-19 20:32:56 UTC 4h 17min ago apt-daily.timer         apt-daily.service

2 timers listed.</code></pre>

### [Viewing Systemt Timers Logs Using Journalctl](#viewing-systemt-timers-logs-using-journalctl)

Unlike with Systemd services, which offer a `SyslogIdentifier= ` setting, timers themselves do not have such a setting and will always log using the “syslog” identifier. To view all logs from the journal that match the syslog field:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">journalctl -f _COMM=systemd</code></pre>
