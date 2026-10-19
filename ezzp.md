---
date: 2026-10-19
keywords: [nvim]
---
# :Tutor

`escape u` will undo the previous change.

`escape : q !` will quit the editor and discard any changes.

The following commands are done within Normal mode:

-   `x` will delete the character under the cursor.
-   `i` will insert text.
-   `A` will append text.
-   `w q` writes a file before quitting.
-   `d w` deletes a word.
-   `d $` deletes until the end of the line. Note that you must place your
    cursor after the final character you want to keep. In the example given,
    `Somebody typed the end of this line twice. end of this line twice.`, you
    must place your cursor after the first period.
-   Commands that change text are made up of an operator and a motion. `d` is
    an operator whereas `$` is a motion. As per the tutorial:

    ```text
    w - until the start of the next word, EXCLUDING its first character.
    e - to the end of the current word, INCLUDING the last character.
    $ - to the end of the line, INCLUDING the last character.
    ```

    Pressing just the motion without an operator will move the cursor
    accordingly.

-   Typing a number before a motion repeats it that many times. `0` will move
    the cursor to the start of the line.
-   Typing an operator, a number, and then a motion will repeat that operator
    that many times.
-   `d d` will delete the whole line; `2 d d` will delete two lines.
-   `u` will undo a previous change and `U` will bring a line to its original
    state. `control r` will redo an undo.

The following is a summary of the above:

```text
 1. To delete from the cursor up to the next word type:    `dw`{normal}

 2. To delete from the cursor to the end of a line type:   `d$`{normal}

 3. To delete a whole line type:                           `dd`{normal}

 4. To repeat a motion prepend it with a number:           `2w`{normal}

 5. The format for a change command is:

        operator   [number]   motion

    where:

        operator -   is what to do, such as [d](d) for delete
        [number] -   is an optional count to repeat the motion
        motion   -   moves over the text to operate on, such as:
                        [w](w) (word),
                        [$]($) (to the end of line), etc.

 6. To move to the start of the line use a zero: [0](0)

 7. To undo previous actions, type:            `u`{normal}  (lowercase u)
    To undo all the changes on a line, type:   `U`{normal}  (capital U)
    To undo the undos, type:                   `<C-r>`{normal}
```

> [!NOTE]
> The following commands always assume you are within Normal mode, unless
> specified otherwise.[^1]

`p` will place the previously deleted text after the cursor; `P` will place the
text before the cursor.

`rx` will replace the character under the cursor with `x`.

`ce` will change until the end of the word. Navigate to the word you want to
change and move the cursor to the first character you want to change. Type `ce`
followed by the text you want to insert instead. `ce` deletes the text and
places you in Insert mode.

The change (`c`) operator behaves exactly like the delete (`d`) operator (i.e.,
uses the same motions and can be used in conjunction with numbers to repeat the
operation).

The following is a summary of the above:

```text
 1. To put back text that has just been deleted, type [p](p). This puts the
    deleted text AFTER the cursor (if a line was deleted it will go on the
    line below the cursor).

 2. To replace the character under the cursor, type [r](r) and then the
    character you want to have there.

 3. The [change operator](c) allows you to change from the cursor to where
    the motion takes you. Type `ce`{normal} to change from the cursor to the
    end of the word, `c$`{normal} to change to the end of a line, etc.

 4. The format for change is:

        c   [number]   motion
```

`/` followed by a phrase will search for the phrase forward from where you are
(note that it will still highlight the phrase in the backward direction) - `?`
can be used to search for a phrase in the backward direction. Use `n` to go
through the phrases in the direction of the search (i.e., forward for `/` or
backward for `?`) and `N` to go in the opposite direction.

`control o` will bring you back to where you came from whereas `control i` will
go forward (if you have gone back via `control o`).

Place your cursor on any `(`, `[`, or `{`, press `%`, and it will bring you to
its matching `)`, `]`, or `}`.

[^1]: I am too lazy to fix the previous explanations to follow this.
