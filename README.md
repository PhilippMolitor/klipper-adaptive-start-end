# Klipper Adaptive Start and End

# IMPORTANT NOTICE

This set of macros is still in alpha state and very much untested. Running this on your expensive fancy printer is completely at your own risk and I cannot give any warranty for my macros working as intended. **_Here be dragons_**.

# Printer feature support

- XYZ Homing (of course)
- Chamber / bed fans
- Bed heat soaking
- Max extruder temperature for probes that use the nozzle
- Z tilt adjust
- Quad gantry leveling
- Mesh bed leveling
- Purging

# Klipper side installation (MainsailOS / Moonraker)

1. SSH into your Klipper host and run these commands:

   ```bash
    cd
    git clone https://github.com/PhilippMolitor/Klipper-Adaptive-Start-End.git
    ln -s ~/Klipper-Adaptive-Start-End/config ~/printer_data/config/KASE
    cp ~/Klipper-Adaptive-Start-End/config/KASE.cfg ~/printer_data/config/KASE.cfg
   ```

2. If you want automatic updates (recommended), add KASE to the `moonraker.conf` file:
   ```ini
    [update_manager Klipper-Adaptive-Start-End]
    type: git_repo
    channel: dev
    primary_branch: main
    origin: https://github.com/PhilippMolitor/Klipper-Adaptive-Start-End.git
    path: ~/Klipper-Adaptive-Start-End
    managed_services: klipper
   ```
3. Finally, add KASE to your `printer.cfg`:
   ```ini
   [include KASE.cfg]
   ```

# Setting up the slicer printer profile

## Cura

Start:

```gcode
PRINT_START TEMP_EXTRUDER={material_print_temperature_layer_0} TEMP_BED={material_bed_temperature_layer_0} TEMP_CHAMBER={build_volume_temperature}
```

End:

```gcode
PRINT_END
```

## PrusaSlicer

Start:

```gcode
M104 S0
M140 S0
PRINT_START TEMP_EXTRUDER=[first_layer_temperature[initial_extruder]] TEMP_BED=[first_layer_bed_temperature]
```

End:

```gcode
PRINT_END
```

## OrcaSlicer

Start:

```gcode
M104 S0
M140 S0
PRINT_START TEMP_EXTRUDER=[first_layer_temperature] TEMP_BED=[first_layer_bed_temperature] TEMP_CHAMBER=[chamber_temperature]
```

End:

```gcode
PRINT_END
```

# Configuration

See `config/KASE.cfg` for reference, and comment in the variables you need. Further explanations of the settings will be added here in the future.

# Support of other macros

1. [KAMP](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging)<br>
   Supports detection of `LINE_PURGE` and `VORON_PURGE`.
2. [Elli's Bed Fans](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/Ellis/Bed_Fans)<br>
   Uses the default `M140` and `M190` G-Codes, so Elli's Bed Fan Macros will automatically work.
3. [StealthBurner Macros](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/Ellis/Bed_Fans)<br>
   Updates the StealthBurner status LED automatically.

# Updating

When updating, you should check the contents of this repo's `config/KASE.cfg` as its contents may have changed, e.g. when introducing new variables. Your existing `KASE.cfg` won't be changed when updating as it is a copy of the one from the moonraker managed directory.

# Improvements to be done

- PrusaSlicer does not support aux fans, so maybe find a good default here that also works with the folks who want to print PLA. Maybe put Chamber Heating into a seperate macro so it can be controlled from the Mainsail UI.
- Elli's Bed Fan Macros are replacing the default G-Codes for heating, so even when printing PLA they do start the bed fans to increase the chamber temperature.
- Add support for Beacon meshing

# Credits

- [Klipper Adaptive Meshing and Purging](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging) by kyleisah
- [A better print_start macro](https://github.com/jontek2/A-better-print_start-macro) by jontek2
