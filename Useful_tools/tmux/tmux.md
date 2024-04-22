---
tags:
  - Useful_tools
---
# Core principles
_tmux stands for terminal multiplexer_
It's a tool which acts as a terminal multiplexer for [[UNIX|UNIX-like]] [[Operating System|Operating Systems]]. In other words, this tool allows us to split a single terminal into several sub-terminals.
It's also very useful for keeping processes running on remote hosts even though you close your session with them.
![[tmux-example.png]]
## Concepts
### tmux client
_In other words, the terminal itself_
Whenever we launch a new terminal, that terminal is the tmux client:
![[tmux-clients-example.png]]
### tmux session
In order to run the [[#tmux client]], you need to [[#Create and start a new session|start a new session]]. After starting it, the [[#tmux server]] will automatically manage that session.
### tmux server
The process that is running on our machine (```localhost```) to provide the tmux functionality. It manages the [[#tmux session|tmux sessions]].
### tmux window
Is the current window you are seeing. You can have several windows in paralell and [[#Change displaying window|change the displaying window]] whenever you want.
A [[#tmux session]] is composed by one or more tmux windows. By default, the session will contain one window. You can [[#Create a new window inside a session|create as many as you want]].
Each window has got a number identifier that is used for switching the displaying window of your session (by default, they are indexed from 0 included).
#### Example
![[tmux-window-example.png]]
Inside ```my_session``` we have two windows. We can see them on green bar below:
![[tmux-windowIDs-example.png]]
- ```0:bash```
- ```1:bash```
### tmux pane
It is a region inside a [[#tmux window]] where we can run commands.
# Hands on
## Basic commands
### Install tmux
#### Linux
```shell
sudo apt-get install tmux
```
### Sessions
#### Create and start a new session
```shell
tmux new -s <session_name>
```
#### List sessions
```shell
tmux ls
```
#### Attach to an existing session
```shell
tmux attach-session -t <session_name>
```
#### Delete all sessions
```shell
tmux kill-server
```
### Windows
#### Create a new window inside a session
- Press ```Ctrl+B``` + ```C```
#### Change displaying window
- Press ```Ctrl+B``` + ```<window_number>```
#### Exit from window (and session, if the window is the last one)
```shell
exit
```
### Panes
#### Create a new pane inside a window
-  Press ```Ctrl+B``` + ```%``` to create a new pane at the right of the selected one
-  Press ```Ctrl+B``` + ```"``` to create a new pane below the selected one
#### Change to another pane
-  Press ```Ctrl+B``` + ```↑```, ```Ctrl+B``` + ```↓```, ```Ctrl+B``` + ```←``` or ```Ctrl+B``` + ```→``` to create a navigate between panes
#### Exit from pane
```shell
exit
```
#### Force-Delete a pane
- Press ```Ctrl+B``` + ```:```. Then, run ```kill-pane``` command
#### Copy and paste
##### Copy something inside tmux pane to paste inside tmux pane
- Press ```Ctrl+B``` + ```[``` and select the text portion you want to copy
- Press ```Ctrl+B``` + ```]``` to paste inside **tmux pane**
##### Copy/Paste from/to System Clipboard
You can hold ```Shift``` to enter terminal mode on tmux. In other words, this means that your tmux will turn into a terminal mode, as if It was a normal terminal. Then, you can either copy something inside tmux to system clipboard (therefore, to paste out of tmux) or copy something outside, on system clipboard, and then paste it inside tmux pane.
For instance, while you are pressing ```Shift```, you can select the text inside tmux and then press right click and Copy the content. Similarly, you can hold ```Shift```, then right click and press Paste to paste content from system clipboard into tmux.
## Configuration
You can make general settings of tmux on ```~/.tmux.conf``` configuration file. If It doesn't exist, [[UNIX#touch (Create new file)|create it]] manually.
_Note: If changes are not being applied, it might be due to another active sessions (of another users). You can force reload the configuration file in your tmux session with `Ctrl+B + : -> source-file <path_to_.tmux.conf>`_
### Enable mouse scrolling on tmux panes
A very useful setting is enabling the mouse scrolling over tmux panes. To do so, add this line to ```~/.tmux.conf```:
```conf
setw -g mouse on
```
