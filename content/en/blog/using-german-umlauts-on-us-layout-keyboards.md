---
title: "Blog Post About How To use German Umlauts on US Layout Keyboards"
description: "This blog post describes howto use German umlauts on US layout keyboards with Ubuntu and Debian Linux workstations, laptops and external USB Keyboards"
date: 2024-01-01
image: "/images/blog/german-flag.webp"
image_alt: "German Flag Using german umlauts (ä,ö,ü,ß,€) on US layout keyboards"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

This post was revived for nostalgy and SEO purposes. It is still up to date though and works on most modern Debian based Linux distributions. The original version can be found on [archive.org](https://web.archive.org/web/20150324043206/http://www.blunix.org/using-german-umlauts-on-us-layout-keyboards/).

## Table of contents

- [Creating a ~/.Xmodmap file](#xmodmap)
- [ISO-Latin1 table](#iso-latin1-table)

## [Creating a ~/.Xmodmap file](#xmodmap)

In order to map key combinations to specific special characters you have to create a .Xmodmap file within your users home directory. First we have to find out what the keycodes of the keys you want to map are. For this enter "xev” and press the appropriate key. You will get an output like this:

```bash
KeyPress event, serial 27, synthetic NO, window 0x1400001,
    root 0xaf, subw 0x0, time 6113848, (451,491), root:(451,511),
    state 0x0, keycode 135 (keysym 0xff7e, Mode_switch), same_screen YES,
    XLookupString gives 0 bytes:
    XmbLookupString gives 0 bytes:
    XFilterEvent returns: False
```

On line 3 you can see "keycode 135", which corresponds to the left windows key on my thinkpad x201s keyboard. Now write the following into your ~/.Xmodmap file:

```bash
! Map umlauts to PRINT SCREEN +
keycode 135 = Mode_switch
keysym e = e E EuroSign
keysym a = a A adiaeresis Adiaeresis
keysym o = o O odiaeresis Odiaeresis
keysym u = u U udiaeresis Udiaeresis
keysym s = s S ssharp
```

To load your new configuration run this command:

```bash
xmodmap ~/.Xmodmap
```

## [ISO-Latin1 table](#iso-latin1-table)

```bash
Symbol number   Symbol  keysym        compose   PostScript    HTML-Code

160 0xa0                nobreakspace  spc spc                  
161 0xa1        ¡       exclamdown      ! !
162 0xa2        ¢       cent            C /
163 0xa3        £       sterling        L -
164 0xa4        ¤       currency        o x     /currency
165 0xa5        ¥       yen             Y -
166 0xa6        ¦       brokenbar       | |     /brokenbar
167 0xa7        §       section         S O     /section
168 0xa8        ¨       diaeresis       " "     /dieresis
169 0xa9        ©       copyright       C O     /copyright     ©   ©
170 0xaa        ª       ordfeminine     - A     /ordfeminine
171 0xab        «       guillemotleft   < <     /guillemotleft
172 0xac        ¬       notsign         - |     /logicalnot
173 0xad        ­        hyphen          - -     /hyphen
174 0xae        ®       registered      R O     /registered    ®    ®
175 0xaf        ¯       macron          ^ -     /macron
176 0xb0        °       degree          ^ *     /degree        °    °
177 0xb1        ±       plusminus       + -     /plusminus
178 0xb2        ²       twosuperior     ^ 2     /twosuperior
179 0xb3        ³       threesuperior   ^ 3     /threesuperior
180 0xb4        ´       acute                 /acute
181 0xb5        µ       mu              / u     /mu
182 0xb6        ¶       paragraph       P !     /paragraph
183 0xb7        ·       periodcentered  ^ .     /periodcentered
184 0xb8        ¸       cedilla         , ,     /cedilla
185 0xb9        ¹       onesuperior     ^ 1     /onesuperior
186 0xba        º       masculine       _ O     /ordmasculine
187 0xbb        »       guillemotright  > >     /guillemotright
188 0xbc        ¼     onequarter      1 4     /onequarter
189 0xbd        ½     onehalf         1 2     /onehalf
190 0xbe        ¾     threequarters   3 4     /threequarters
191 0xbf        ¿       questiondown    ? ?     /questiondown
192 0xc0        À       Agrave          A `     /Agrave        À À
193 0xc1        Á       Aacute          A '     /Aacute        Á Á
194 0xc2        Â       Acircumflex     A ^     /Acircumflex   Â  Â
195 0xc3        Ã       Atilde          A ~     /Atilde        Ã Ã
196 0xc4        Ä       Adiaeresis      A "     /Adieresis     Ä   Ä
197 0xc5        Å       Aring           A *     /Aring         Å  Å
198 0xc6        Æ       AE              A E     /AE            Æ  Æ
199 0xc7        Ç       Ccedilla        C ,     /Ccedilla      Ç Ç
200 0xc8        È       Egrave          E `     /Egrave        È È
201 0xc9        É       Eacute          E '     /Eacute        É É
202 0xca        Ê       Ecircumflex     E ^     /Ecircumflex   Ê  Ê
203 0xcb        Ë       Ediaeresis      E "     /Edieresis     Ë   Ë
204 0xcc        Ì       Igrave          I `     /Igrave        Ì Ì
205 0xcd        Í       Iacute          I '     /Iacute        Í Í
206 0xce        Î       Icircumflex     I ^     /Icircumflex   Î  Î
207 0xcf        Ï       Idiaeresis      I "     /Idieresis     Ï   Ï
208 0xd0        Ð       ETH             D -     /Eth
209 0xd1        Ñ       Ntilde          N ~     /Ntilde        Ñ Ñ
210 0xd2        Ò       Ograve          O `     /Ograve        Ò Ò
211 0xd3        Ó       Oacute          O '     /Oacute        Ó Ó
212 0xd4        Ô       Ocircumflex     O ^     /Ocircumflex   Ô  Ô
213 0xd5        Õ       Otilde          O ~     /Otilde        Õ Õ
214 0xd6        Ö       Odiaeresis      O "     /Odieresis     Ö   Ö
215 0xd7        ×       multiply        x x     /multiply
216 0xd8        Ø       Ooblique        O /     /Oslash        Ø Ø
217 0xd9        Ù       Ugrave          U `     /Ugrave        Ù Ù
218 0xda        Ú       Uacute          U '     /Uacute        Ú Ú
219 0xdb        Û       Ucircumflex     U ^     /Ucircumflex   Û  Û
220 0xdc        Ü       Udiaeresis      U "     /Udieresis     Ü   Ü
221 0xdd        Ý       Yacute          Y '     /Yacute        Ý Ý
222 0xde        Þ       THORN           P |     /Thorn
223 0xdf        ß       ssharp          s s     /germandbls    ß  ß
224 0xe0        à       agrave          a `     /agrave        à à
225 0xe1        á       aacute          a '     /aacute        á á
226 0xe2        â       acircumflex     a ^     /acircumflex   â  â
227 0xe3        ã       atilde          a ~     /atilde        ã ã
228 0xe4        ä       adiaeresis      a "     /adieresis     ä   ä
229 0xe5        å       aring           a *     /aring         å  å
230 0xe6        æ       ae              a e     /ae            æ  æ
231 0xe7        ç       ccedilla        c ,     /ccedilla      ç ç
232 0xe8        è       egrave          e `     /egrave        è è
233 0xe9        é       eacute          e '     /eacute        é é
234 0xea        ê       ecircumflex     e ^     /ecircumflex   ê  ê
235 0xeb        ë       ediaeresis      e "     /edieresis     ë   ë
236 0xec        ì       igrave          i `     /igrave        ì ì
237 0xed        í       iacute          i '     /iacute        í í
238 0xee        î       icircumflex     i ^     /icircumflex   î  î
239 0xef        ï       idiaeresis      i "     /idieresis     ï   ï
240 0xf0        ð       eth             d -     /eth
241 0xf1        ñ       ntilde          n ~     /ntilde        ñ ñ
242 0xf2        ò       ograve          o `     /ograve        ò ò
243 0xf3        ó       oacute          o '     /oacute        ó ó
244 0xf4        ô       ocircumflex     o ^     /ocircumflex   ô  ô
245 0xf5        õ       otilde          o ~     /otilde        õ õ
246 0xf6        ö       odiaeresis      o "     /odieresis     ö   ö
247 0xf7        ÷       division        - :     /divide
248 0xf8        ø       oslash          o /     /oslash        ø ø
249 0xf9        ù       ugrave          u `     /ugrave        ù ù
250 0xfa        ú       uacute          u '     /uacute        ú ú
251 0xfb        û       ucircumflex     u ^     /ucircumflex   û  û
252 0xfc        ü       udiaeresis      u "     /udieresis     ü   ü
253 0xfd        ý       yacute          y '     /yacute        ý ý
254 0xfe        þ       thorn           p |     /thorn
255 0xff        ÿ       ydiaeresis      y "     /ydieresis     ÿ   ÿ
```
