Attempt to reverse engineer SNES FFV game.

The project's objective - overview through the entire game code, locate key features and document it. Of particular interest is the combat system and the behavior of monsters in battle.

http://www.gamefaqs.com/snes/588331-final-fantasy-v/faqs/30040

<img src="http://psxdev.ru/images/wys/ffv_title_screen.jpg">

**Game engine parts**

<table>
<tr><td><nobr><img src="http://psxdev.ru/images/wys/ffv_world.jpg" width=150px>
  <img src="http://psxdev.ru/images/wys/ffv_field.jpg" width=150px></nobr></td>
  <td><img src="http://psxdev.ru/images/wys/ffv_battle.jpg" width=250px></td>
  <td><img src="http://psxdev.ru/images/wys/ffv_menu.jpg" width=250px></td>
  <td><img src="http://psxdev.ru/images/wys/ffv_bgfx.jpg" width=250px></td>
  <td><img src="http://psxdev.ru/images/wys/nobuo.jpg" width=250px></td>
 </tr>
 <tr><td>WorldMap+Field+Script=Core C0:0000</td>
 <td>Battle C1:0000, C2:0000</td>
 <td>Menu C2:A000</td>
 <td>BGFX C3:0000</td>
 <td>SPC Engine C4:0000</td>
 </tr>
 </table>

**Sound engine**

  * C4:0000 - SPC init
  * C4:0004 - write to [00:1D00] 4 bytes before call: 
    * 01 nn 08 0F: play music #nn
    * 02 nn 0F 88: play SFX #nn
    * 80 40 08 0F: fade-out ?
    * F0 ?? ?? ??: SPC Engine soft reset
    * F2 22 08 0F: ??

**ROM information**

Used ROM : Final Fantasy V (J) [T+Eng1.1_RPGe].smc

(english translation by RGPe)

No SMC 512-byte header.

**ROM header (0xFFC0)**

  * Game title : "FINAL FANTASY 5      "
  * ROM makeup byte : 21, 0b00100001, [HiROM]
  * ROM type: 02
  * ROM size: 0B
  * SRAM size: 03
  * Creator license ID code: 00 C3
  * Version #: 00
  * Checksum complement: 0D E0
  * Checksum: F2 1F

**Interrupt vectors**

  * NMI: 0xCEE0
  * IRQ: 0xCEE4
  * [RESET]: 0xCEC0

**Used tools**

  * IDA for snes
  * no$sns debugger (http://problemkaputt.de/sns.htm)

SNES Regs : http://en.wikibooks.org/wiki/Super_NES_Programming/SNES_Hardware_Registers

http://nocash.emubase.de/fullsnes.htm

**IDA and CPU M/X Flags**

SNES CPU has weird flags in Native mode.

  * REP #0x10 : Clear X Flag (X/Y Index registers are 16-bit)
  * REP #0x20 : Clear M Flag (A register is 16-bit)
  * SEP #0x10 : Set X Flag (8-bit X/Y regs)
  * SEP #0x20 : Set M Flag (8-bit A register)

These settings affect instruction size and sometimes IDA 65816 module fail to analyze code.

To set those flags manually - press Alt+G to change "segment registers". CPU module will map M/X flags as m/x segment regs,
so you can change it manually.

To check current segregs values press Ctrl+Space.

**Asm hints**

  * BCC reg, imm : if ( reg < imm)
  * BCS reg, imm : if ( reg >= imm )
  * BMI reg : if ( reg & 0x80 )  /  if ( reg >= 0x80 )
  * BPL reg : if ( reg < 0x80 )
