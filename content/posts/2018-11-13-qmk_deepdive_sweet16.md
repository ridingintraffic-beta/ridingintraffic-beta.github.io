---
layout: single
title: 2018-11-13 QMK-deepdive-sweeet16
modified:
categories: blog
date: 2018-11-13T08:08:50-04:00
---

# QMK deepdive using a sweeet16
16 keys!? What can be done with 16 keys for a keyboard? Using an amazing open source firmware and a kit by 1upkeyboards, much can be done with only 16 keys. QMK is a powerhouse of a firmware that allows an endless combination of regular keys, functions and extended macros. These features are relatively easy to construct and the firmware itself can be very easy to build and flash.

First things first what is a sweet16? It is a macropad sold as a kit, made by 1upkeyboards. This macropad requires assembly, which includes soldering in the diodes, arduino and switches. All and all the assembly should take from 20min to an hour depending on skillset and haste.
[Sweet 16 Macro Pad - 1Up Keyboards](https://www.1upkeyboards.com/shop/keyboard-kits/sweet16-macro-pad-white/ "Sweet 16 Macro Pad - 1Up Keyboards")


an assembly video can be found here: [youtu.be/Bw-BvKnZqBg](https://youtu.be/Bw-BvKnZqBg "youtu.be/Bw-BvKnZqBg") 
(embedding youtube doesn't work in medium) Truncated assembly photos are included here <--
The video is the best source of a step by step walkthrough assembling the pad. The goal of the article today is not to walk through the hardware assembly but more about the software.

The macropad has now been assembled and there is a supplied basic firmware that will give the functionality of a simple numpad. The hex file is here: [sweet16.hex ](https://1upkeyboards.com/sweet16.hex "sweet16.hex ")  QMK has a great flashing program called QMK toolbox, it is available for mac and pc and can be downloaded here. [qmk.fm/toolbox/](https://qmk.fm/toolbox/ "qmk.fm/toolbox/") It is as easy as downloading the toolbox running the application and then plugging in the sweet16 macropad. First open the qmk toolbox app and load the hex file that was downloaded above. Then plug in the pad and there is a reset switch on the back, press that and the toolbox registers a new device, then just click flash, wait a few seconds and wait for “done”.


after hitting reset

the flash is complete
The point of the simple hex firmware is to ensure that all of the switches are working properly and the assembly was successful. It can be a bit tricky to make sure all the solder points are good. Therefore, a simple keypad debug is the easiest way to confirm assembly.

The hardware is assembled correctly and everything works. Now on to the fun part. Building the custom firmware. The qmk docs are great for explaining everything about the firmware and the source code is, open source. Links to the firmware and docs are below. It is highly suggested, reading through all of these.

qmk/qmk_firmware

keyboard controller firmware for Atmel AVR and ARM USB families - qmk/qmk_firmware
github.com	
QMK Firmware

Description
docs.qmk.fm	
Great, welcome back. The three main files that are modified when working with qmk firmware are all found in the folder of the specific keyboard. in this cases its keyboards/1upkeyboards/sweet16 The important files are config.h keymap.c rules.mk The config.h is where global variables are setup. The rules.mk is where functionally is enabled or disabled, such as turn on RGB underglow or “tap dance”. Finally the keymap.c is where the magic happens, this is the file that defines what the keys do and has the custom functions.

The firmware that is referenced can be found as a part of the master branch for QMK (medium doesn't want to embed) yey! author, open source contribution ftw [sweet16/keymaps/ridingintraffic/keymap.c](https://raw.githubusercontent.com/qmk/qmk_firmware/master/keyboards/1upkeyboards/sweet16/keymaps/ridingintraffic/keymap.c "sweet16/keymaps/ridingintraffic/keymap.c")

```
...

const uint16_t PROGMEM keymaps[][MATRIX_ROWS][MATRIX_COLS] = {

    /* EMOJI Pad
    * ,-------------------------------.
    * |TFLIP | TFlIP2|DISFACE|   FU   |
    * |------+-------+-------+--------|
    * | CLOUD|       |       | CLEAR  |
    * |------+-------+-------+--------|
    * |SHRUG |DISFACE| HRTFAC| HAPPYF |
    * |------+-------+-------+--------|
    * | ENTER|       |LEDCNTR| tapland| 
    * `-------------------------------'
    */
      //purple
    [_EMOJI] = LAYOUT_ortho_4x4(
       TFLIP,   TFLIP2,   KC_NO,      FU , 
       CLOUD,   KC_NO,  KC_NO,      CMDCLEAR, 
       SHRUG,   DISFACE,  HEARTFACE,    HAPPYFACE, 
       KC_ENT,  RGB_TOG,  MO(_LEDCNTL), MO(_TAPLAND)
    ),
    ```

This first snippet is an example of the basic key layout. In the real firmware the variables initialization for all the custom keys is done at the top, but here is where the grid of keys are assigned and the “odd” names of tflip and disface are used, wat? This is the fun part, these odd keynames are actually functions that invoke sending hexcode combinations through the keyboard for that single key press which will print an inline ascii art reaction emoji.

tflip: (╯°□°)╯ ︵ ┻━┻
tflip2: ┻━┻︵ \(°□°)/ ︵ ┻━┻
fu: t(-_-t)
cloud: (っ◕‿◕)っ
shrug: ¯\_(ツ)_/¯
disface: ಠ_ಠ
heartface: ♥‿♥
happyface: ʘ‿ʘ
These custom keycodes when pressed, trigger a function that passes the hexcodes. This is done simply below
```
bool process_record_user(uint16_t keycode, keyrecord_t *record) {
    if (record->event.pressed) {
        switch(keycode) {
            case CLOUD:       // (っ◕‿◕)っ
                if(record->event.pressed){
                    send_unicode_hex_string("0028 3063 25D5 203F 25D5 0029 3063");
                }
                return false;
                break;
            case FU:       // t(-_-t)
                if(record->event.pressed){
                    SEND_STRING("t(-_-t)");
                }
                return false;
                break;  
            case HAPPYFACE:       // ʘ‿ʘ 
                if(record->event.pressed){
                     send_unicode_hex_string("0298 203F 0298");
                }
                return false;
                break; 
            case CMDCLEAR:
                if (record->event.pressed) {
                    register_code(KC_LGUI);
                    tap_code(KC_A);                 
                    unregister_code(KC_LGUI);
                    tap_code(KC_DEL);                 
                }
                return false;
                break;  
            case SHRUG: // ¯\_(ツ)_/¯
                if (record->event.pressed) {
                    send_unicode_hex_string("00AF 005C 005F 0028 30C4 0029 005F 002F 00AF");
                }
                return false; 
                break;
            case HEARTFACE:       // ♥‿♥
                if(record->event.pressed){
                    send_unicode_hex_string("2665 203F 2665");
                }
                return false;
                break;  
            case DISFACE:       // ಠ_ಠ 
                if(record->event.pressed){
                    send_unicode_hex_string("0CA0 005F 0CA0");
                }
                return false;
                break;
            case TFLIP:         // (╯°□°)╯ ︵ ┻━┻ 
                if(record->event.pressed){
                    send_unicode_hex_string("0028 256F 00B0 25A1 00B0 0029 256F 0020 FE35 0020 253B 2501 253B");
                }
                return false;
                break;
            case TFLIP2:         // ┻━┻︵ \(°□°)/ ︵ ┻━┻  
                if(record->event.pressed){
                    send_unicode_hex_string("253B 2501 253B FE35 0020 005C 0028 00B0 25A1 00B0 0029 002F 0020 FE35 0020 253B 2501 253B");
                }
                return false;
                break;
                } 
    }
    return true;
}
```


Simple enough, now the macropad has simple functionality and has been filled up with reaction emojis. Some would find that useful others may see it as a waste. That is where the concept of layering comes in. Documentation of course is available explaining exactly how layering works [switching-and-toggling-layers](https://docs.qmk.fm/#/feature_advanced_keycodes?id=switching-and-toggling-layers "switching-and-toggling-layers") Think of layers like switching from upper case to lower case, or the symbols that are in the number row. There is the ability to toggle between layers much like capslock, however it can be easier to just have a shift key style layering called “momentary” or “mo” layering, where the layer is only active while a key is held. Either way that now enables a whole new set of functions to be added to the keys while only sacrificing a single key to switch between the layers.

Onto the final chapter, “tap dance”. This is where things get a little weird. With normal keyboards and normal experiences a single keypress enters a single key, and a quicker double keypress enters two of those keys. With qmk and tapdance this all changes because when enabled and coded, the tap dance functionality will say “when pressed once” output a , when “pressed twice in 500ms” output super happy fun guy . The example is below
```
keymap.c
....
/* tap dance time */
void tdexample1(qk_tap_dance_state_t *state, void *user_data) {
  if (state->count >= 2) {
    SEND_STRING(EXAMPLESTRING1);
    reset_tap_dance (state);
  }
}
void tdexample2(qk_tap_dance_state_t *state, void *user_data) {
  if (state->count >= 2) {
    SEND_STRING(EXAMPLESTRING2);
    reset_tap_dance (state);
  }
}
void tdexample3(qk_tap_dance_state_t *state, void *user_data) {
  if (state->count >= 2) {
    SEND_STRING(EXAMPLESTRING3);
    reset_tap_dance (state);
  }
}
void tdexample4(qk_tap_dance_state_t *state, void *user_data) {
  if (state->count >= 2) {
    SEND_STRING(EXAMPLESTRING4);
    reset_tap_dance (state);
  }
}

qk_tap_dance_action_t tap_dance_actions[] = {
    [TD_EXAMPLE1] = ACTION_TAP_DANCE_FN(tdexample1),
    [TD_EXAMPLE2] = ACTION_TAP_DANCE_FN(tdexample2),
    [TD_EXAMPLE3] = ACTION_TAP_DANCE_FN(tdexample3),
    [TD_EXAMPLE4] = ACTION_TAP_DANCE_FN(tdexample4)
};

... 

config.h
...


#define EXAMPLESTRING1 "tapdance_1"
#define EXAMPLESTRING2 "tapdance_2"
#define EXAMPLESTRING3 "tapdance_3"
#define EXAMPLESTRING4 "tapdance_4"
```

Here the keycode TD_EXAMPLE1 is bound to the tapdance function tdexample1 and then that function sends the string that was defined in config.h that outputs tapdance_1 . It sounds a little complicated but break it down and then its simple, tap the key once get an emjoi, double tap the key get a whole string. Add in a layer shift and then there is an entire new functionality behind the key. And it is done The sweet16 macropad is now massively more useful than just a single numpad with 16 buttons. The macropad has enough memory to do at least 4+ layers like this. At which point it is pretty tough to keep all the functions memorized.

But thats not all. QMK comes equipped with a Dockerfile. Which means building the firmware is as simple as having docker installed and then running the build process from docker. An example command would be: docker run -e keymap=ridingintraffic -e keyboard=1upkeyboards/sweet16 — rm -v $('pwd'):/qmk:rw edasque/qmk_firmware Through the power of docker it is volume mounting the working directory with all of the keyboard keymaps, into the image and then all of the build dependancies are prebuilt in the image and the Ccode is then converted into a hex file in the root folder of QMK. No muss no fuss just a clean build environment and the compiled hex files for the keyboard. Anyone that has tried to compile firmware before, knows that this is a massive time savings and the reproducibility is incredible.

Thats it! A macropad and an entire open source firmware that supports over 120 keyboards. QMK and the open source community behind it are wonderful. There is a discord chat as well if chatting to a human is helpful when stuck, or let me know and I can help out wherever I can.