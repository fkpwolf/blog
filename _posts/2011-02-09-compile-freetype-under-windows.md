---
title: Compile FreeType under Windows
author: Fan Fan
layout: post
permalink: /2011/02/09/compile-freetype-under-windows/
dsq_thread_id:
  - 656682047
categories:
  - font
---
1. Use MinGW as unix-style environment. It is recommended by freetype.org, rather than Cygwin. Maybe for better performance.

2. Use **mingw-get install autoconf automake** to install missed packages which is not included by default installation.

3. git clone code from [here][1]

4. first compile freetype2, then freetype2-deoms. When compiling demos, modify file freetype2-demos\graph\rules.mk to add one line as below:  
`PLATFORM=win32<br />
include $(wildcard $(TOP_DIR_2)/graph/*/rules.mk)`

If you use **make PLATFORM=win32** at root directory, it will build failed. Maybe the MinGW is not standard win32 system, so we have to build graph lib separately at win32 mode. Otherwise generated executable file will complain that &#8220;*could not allocate display surface&#8221;.*

5. in root directory, launch with:  
`./bin/ftview -m wokao 33 ../ttf-bitstream-vera-1.10/Vera.ttf`

[<img class="alignnone size-full wp-image-910" title="freetype" src="http://fkpwolf.net/WordPress/wp-content/uploads/2011/02/freetype.png" alt="" width="506" height="430" />][2]

 [1]: http://git.savannah.gnu.org/cgit/freetype/
 [2]: http://fkpwolf.net/WordPress/wp-content/uploads/2011/02/freetype.png