## Cheatsheet

- `nano -m <filename>` - using mouse inside nano
    - Place the cursor on a position by left-clicking on that position.
    - Mark text by holding Shift and drag with left click.
- `nano -q <filename>` - displaying a scrollbar
- `nano -l <filename>` - showing line numbers
    - **Meta(Alt) + N** - toggle the line numbers
- `nano --minibar <filename>` - placing a minibar
    - File name (with an asterisk, if there are any unsaved modifications).
    - When first opened, it shows the number of lines in the file. This disappears if you press any key.
- `nano -0 <filename>` - going distraction free
    - This will hide the title bar, status bar and the help section. Any important message will appear on the bottom status bar part, and will disappear in 1.5 seconds after pressing any key.
    - **Meta + Z**: Toggle Title and Status bar.
    - **Meta + X**: Toggle the Help section at the bottom.
    - **Meta(Alt) + C** to get some more details like, current cursor position, how deep you are in the document (in percentage, based on the line), etc.
- `nano -t <filename>` - automatically saving file at exit
- `nano -B <filename>` - automatically saving a backup version
- `nano +c/pattern <filename>` - placing cursor at a specified position
    - +: Search for the specified word from the beginning of the text. Use - instead, to start searching for the word from the end of the file.
    - c: Small letter c indicates a case-sensitive search.
- `nano -s hunspell <filename>` - checking spelling
    - `sudo apt install hunspell`
    - You may need to verify that the language has properly installed. For this, go to System **Settings → Region and Language**. 
- `nano -v <filename>` - opening file in read-only mode