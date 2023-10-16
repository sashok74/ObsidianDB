C-a :       Prompt for a command			
C-a c       Create a new window
C-a %      Split window horizontally
C-a "       Split window vertically
C-a &      Kill current window
C-a r       source-file
C-a ?       Lislt key bindings


bind -r > resize-pane -R 10
bind -r - resize-pane -D 10
bind -r + resize-pane -U 10

## pane movement shortcuts (same as vim)
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R


C-a C-o     Rotate through the panes                                                                                                                                   (2 results) [22/22]
C-a C-z     Suspend the current client
C-a Space   Select next layout
C-a !       Break pane to a new window
													C-a 0       Select window 0
C-a #       List all paste buffers                  C-a 1       Select window 1
C-a $       Rename current session                  C-a 2       Select window 2
													C-a 3       Select window 3
													C-a 4       Select window 4
C-a '       Prompt for window index to select       C-a 5       Select window 5
C-a (       Switch to previous client               C-a 6       Select window 6
C-a )       Switch to next client                   C-a 7       Select window 7
													C-a 8       Select window 8
C-a .       Move the current window                 C-a 9       Select window 9
C-a /       Describe key binding

C-a ;       Move to the previously active pane				C-a M-1     Set the even-horizontal layout				
C-a =       Choose a paste buffer from a list               C-a M-2     Set the even-vertical layout                
C-a ?       List key bindings                               C-a M-3     Set the main-horizontal layout                
C-a D       Choose a client from a list                     C-a M-4     Set the main-vertical layout                
C-a E       Spread panes out evenly                         C-a M-5     Select the tiled layout                
C-a L       Switch to the last client                       C-a M-n     Select the next window with an alert                
C-a M       Clear the marked pane                           C-a M-o     Rotate through the panes in reverse                
C-a ]       Paste the most recent paste buffer              C-a M-p     Select the previous window with an alert                
C-a c       Create a new window                             C-a M-Up    Resize the pane up by 5                
C-a d       Detach the current client                       C-a M-Down  Resize the pane down by 5                
C-a f       Search for a pane                               C-a M-Left  Resize the pane left by 5                
C-a i       Display window information                      C-a M-Right Resize the pane right by 5                
C-a m       Toggle the marked pane                          C-a C-Up    Resize the pane up                
C-a n       Select the next window                          C-a C-Down  Resize the pane down                
C-a o       Select the next pane                            C-a C-Left  Resize the pane left                
C-a q       Display pane numbers                            C-a C-Right Resize the pane right                
C-a s       Choose a session from a list                    C-a S-Up    Move the visible part of the window up                
C-a t       Show a clock                                    C-a S-Down  Move the visible part of the window down                
C-a w       Choose a window from a list                     C-a S-Left  Move the visible part of the window left                
C-a x       Kill the active pane                            C-a S-Right Move the visible part of the window right                
C-a z       Zoom the active pane
C-a {       Swap the active pane with the pane above
C-a }       Swap the active pane with the pane below
C-a ~       Show messages
C-a DC      Reset so the visible part of the window follows the cursor
C-a PPage   Enter copy mode and scroll up
C-a Up      Select the pane above the active pane
C-a Down    Select the pane below the active pane
C-a Left    Select the pane to the left of the active pane
C-a Right   Select the pane to the right of the active pane

C-a [ (enter copy-mode), Space (start highlighting), Enter (end highlighting), C-a ] (paste)



















