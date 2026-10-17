---
date: 2026-10-16
keywords: [linux]
---
# UPower

> [!NOTE]
> This command only works for laptops.

````ai
! upower -i $(upower -e | grep BAT) | grep -E "state|energy-rate|percentage"
(Bash completed with no output)

The command produced no output, which usually means there's no device matching BAT in upower's list. I'll check what power devices your system reports.

  Ran 1 shell command

Sure. The command is a pipeline of three steps, and you can run each step on its own to see where it breaks.

1. List the power devices upower knows about
upower -e
-e means "enumerate". It prints one object path per device, something like /org/freedesktop/UPower/devices/battery_BAT0 or /org/freedesktop/UPower/devices/line_power_AC.

2. Keep only the battery lines
upower -e | grep BAT
grep BAT keeps only lines containing BAT. On a laptop this usually leaves one path.

3. Show details for that device
upower -i /org/freedesktop/UPower/devices/battery_BAT0
-i means "info". It prints a block of properties.

4. Filter the details
... | grep -E "state|energy-rate|percentage"
-E enables extended regex, and the | inside the quotes means "or". This keeps lines containing any of the three words: state (charging or discharging), energy-rate (the current draw in watts), and percentage.

The $( ... ) wrapper in your original command runs step 2 first and inserts its output into step 3. That's why the whole thing works as one line.

Why yours printed nothing: the output of upower -e | grep BAT was empty, so step 3 had no device to query and grep had nothing to filter. You can confirm by running step 1 alone. If you see no battery_... line, the machine either has no battery (a desktop) or the battery is listed under a different name. Tell me what step 1 prints and I can help interpret it.

✻ Churned for 8s · done 12:27
````
