---
layout: post
title: vim Usage note
---
Introduce Vim usage  
<!--more-->

Basic:
===

   command       |  function  
---------------  |  --------------------    
:e file       | Open filename for edition   
:W            | Save file   
:q            | Exit vim  
:q!           | Quit without saving    
:x            | Write file (if file changed), and exit    
:sav filename | Saves as **filename**   
**.**         | **Repeats the last change made in normal mode**


Move:
===

 command | function  
  ---------------- | --------------------------------------------------|
  h,j,k,l | move the cursor left, down, up, right
  e | the end of the word
  b | the begin of the word
  0 | the begin of the line
  $ | the end of the line
  G | the end of the file
  gg| the beginning of the file
  L | the end of the screen
  :**number** | to line **number**
  **number**\| | to column **number**
  % | to match parenthesis
  [[ | function start
  [{ | block start   
  
  
  ### Copy:
  I


  `"+y` copy to system clipbord  
  `"+p` paste from system clipbord
  
  
  