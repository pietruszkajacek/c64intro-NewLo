
BasicUpstart2(start)

.var pickolor = $5f40
.var ekrancol = $6800

.var fonts    = $3000
.var proll    = $0d38 // $5f38
.var tekst    = $0900

.var ptrchar  = $fb //(2)
.var ptrcol   = $fd //(2)

            * = $2500 "Main Program"
            sei
start:      lda #$3b
            sta $d011
            lda #$c8
            sta $d016
            lda $dd00
            and #$fc
            ora #$02
            sta $dd00
            lda #$a0
            sta $d018
            ldx #$00
cpycol:	    lda pickolor,x
            sta ekrancol,x
            lda pickolor+$0100,x
            sta ekrancol+$0100,x
            lda pickolor+$0200,x
            sta ekrancol+$0200,x
            lda pickolor+$0300,x
            sta ekrancol+$0300,x
            inx
            bne cpycol
            lda #$0c
            sta $d020

            jsr copyline

            lda #$00
            jsr $1000

            jsr clrscroll

petla:      lda #$e0
raster:     cmp $d012
            bne raster
            inc $d020
            jsr $1003
            jsr clrlogo

            //inc $d020
            jsr rolluj

            jsr loadchar

            cmp #$ff
            bne ifspace

            jsr nullwskt
            jmp nxt

ifspace:    cmp #$20
            bne rysuj

            inc spacja

            lda dlspacji
            cmp spacja
            bne nxt

            lda #$00
            sta spacja
            jsr incwskt
            //jsr rstflagp
            //jsr rstwskcol
            jmp nxt

rysuj:      jsr ryskol

            lda #$01
            cmp fclrcol
            beq nxtcol

            jsr incwskt
            jsr rstflagp
            jsr rstwskcol
            jmp nxt

nxtcol:     dec wskcol
            jsr rstflagp
nxt:
            jsr eorline

            //jsr clrscr

            dec $d020
            //dec $d020
            //dec $d020

            jmp petla

//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label eorline  = *
            ldx #$00
e1:         lda $5400,x
            eor $0c00,x
            sta $5400,x
            inx
            bne e1
e2:         lda $5500,x
            eor $0d00,x
            sta $5500,x
            inx
            cpx #$40
            bne e2
            rts
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label clrlogo  = *
            ldx #$00
cl1:        lda $0a00,x
            sta $5400,x
            inx
            bne cl1
cl2:        lda $0b00,x
            sta $5500,x
            inx
            cpx #$40
            bne cl2
            rts
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label copyline = *
            ldx #$00
cli1:       lda $5400,x
            sta $0a00,x
            inx
            bne cli1
cli2:       lda $5500,x
            sta $0b00,x
            inx
            cpx #$40
            bne cli2
            rts
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label rstwskcol = *
            lda #$07
            sta wskcol
            rts
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label rstflagp = *
            lda #$00
            sta fclrcol
            rts
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label ryskol   = *
            lda znak
            sta ptrcol
            lda #$00
            sta ptrcol+1
            asl ptrcol
            rol ptrcol+1
            asl ptrcol
            rol ptrcol+1
            asl ptrcol
            rol ptrcol+1
            clc
            lda #<fonts
            adc ptrcol
            sta ptrcol
            lda #>fonts
            adc ptrcol+1
            sta ptrcol+1
            ldy #$00
nxtpixel: 
            lda (ptrcol),y
            ldx wskcol
            beq bez
right:      lsr
            dex
            bne right
bez:        and #%00000001
            beq zapisz
            sta fclrcol
zapisz:     ora proll,y
            sta proll,y
            iny
            cpy #$08
            bne nxtpixel
            rts
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label nullwskt = *
            lda #<tekst
            sta wsktekst
            lda #>tekst
            sta wsktekst+1
            rts
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label loadchar = *
            lda wsktekst
            sta ptrchar
            lda wsktekst+1
            sta ptrchar+1
            ldy #$00
            lda (ptrchar),y
            sta znak
            rts
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label incwskt = *
            clc
            lda #$01
            adc wsktekst
            sta wsktekst
            bcc *+5
            inc wsktekst+1
            rts
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label clrscroll = *

            lda #$00
            ldx #$00
l1:         sta $0c00,x
            inx
            bne l1
l2:         sta $0d00,x
            inx
            cpx #$40
            bne l2
            rts

//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
.label rolluj = *
            clc
            rol proll
            rol proll+0-8
            rol proll+0-16
            rol proll+0-24
            rol proll+0-32
            rol proll+0-40
            rol proll+0-48
            rol proll+0-56
            rol proll+0-64
            rol proll+0-72
            rol proll+0-80
            rol proll+0-88
            rol proll+0-96
            rol proll+0-104
            rol proll+0-112
            rol proll+0-120
            rol proll+0-128
            rol proll+0-136
            rol proll+0-144
            rol proll+0-152
            rol proll+0-160
            rol proll+0-168
            rol proll+0-176
            rol proll+0-184
            rol proll+0-192
            rol proll+0-200
            rol proll+0-208
            rol proll+0-216
            rol proll+0-224
            rol proll+0-232
            rol proll+0-240
            rol proll+0-248
            rol proll+0-256
            rol proll+0-264
            rol proll+0-272
            rol proll+0-280
            rol proll+0-288
            rol proll+0-296
            rol proll+0-304
            rol proll+0-312
            clc
            rol proll+1
            rol proll+1-8
            rol proll+1-16
            rol proll+1-24
            rol proll+1-32
            rol proll+1-40
            rol proll+1-48
            rol proll+1-56
            rol proll+1-64
            rol proll+1-72
            rol proll+1-80
            rol proll+1-88
            rol proll+1-96
            rol proll+1-104
            rol proll+1-112
            rol proll+1-120
            rol proll+1-128
            rol proll+1-136
            rol proll+1-144
            rol proll+1-152
            rol proll+1-160
            rol proll+1-168
            rol proll+1-176
            rol proll+1-184
            rol proll+1-192
            rol proll+1-200
            rol proll+1-208
            rol proll+1-216
            rol proll+1-224
            rol proll+1-232
            rol proll+1-240
            rol proll+1-248
            rol proll+1-256
            rol proll+1-264
            rol proll+1-272
            rol proll+1-280
            rol proll+1-288
            rol proll+1-296
            rol proll+1-304
            rol proll+1-312
            clc
            rol proll+2
            rol proll+2-8
            rol proll+2-16
            rol proll+2-24
            rol proll+2-32
            rol proll+2-40
            rol proll+2-48
            rol proll+2-56
            rol proll+2-64
            rol proll+2-72
            rol proll+2-80
            rol proll+2-88
            rol proll+2-96
            rol proll+2-104
            rol proll+2-112
            rol proll+2-120
            rol proll+2-128
            rol proll+2-136
            rol proll+2-144
            rol proll+2-152
            rol proll+2-160
            rol proll+2-168
            rol proll+2-176
            rol proll+2-184
            rol proll+2-192
            rol proll+2-200
            rol proll+2-208
            rol proll+2-216
            rol proll+2-224
            rol proll+2-232
            rol proll+2-240
            rol proll+2-248
            rol proll+2-256
            rol proll+2-264
            rol proll+2-272
            rol proll+2-280
            rol proll+2-288
            rol proll+2-296
            rol proll+2-304
            rol proll+2-312
            clc
            rol proll+3
            rol proll+3-8
            rol proll+3-16
            rol proll+3-24
            rol proll+3-32
            rol proll+3-40
            rol proll+3-48
            rol proll+3-56
            rol proll+3-64
            rol proll+3-72
            rol proll+3-80
            rol proll+3-88
            rol proll+3-96
            rol proll+3-104
            rol proll+3-112
            rol proll+3-120
            rol proll+3-128
            rol proll+3-136
            rol proll+3-144
            rol proll+3-152
            rol proll+3-160
            rol proll+3-168
            rol proll+3-176
            rol proll+3-184
            rol proll+3-192
            rol proll+3-200
            rol proll+3-208
            rol proll+3-216
            rol proll+3-224
            rol proll+3-232
            rol proll+3-240
            rol proll+3-248
            rol proll+3-256
            rol proll+3-264
            rol proll+3-272
            rol proll+3-280
            rol proll+3-288
            rol proll+3-296
            rol proll+3-304
            rol proll+3-312
            clc
            rol proll+4
            rol proll+4-8
            rol proll+4-16
            rol proll+4-24
            rol proll+4-32
            rol proll+4-40
            rol proll+4-48
            rol proll+4-56
            rol proll+4-64
            rol proll+4-72
            rol proll+4-80
            rol proll+4-88
            rol proll+4-96
            rol proll+4-104
            rol proll+4-112
            rol proll+4-120
            rol proll+4-128
            rol proll+4-136
            rol proll+4-144
            rol proll+4-152
            rol proll+4-160
            rol proll+4-168
            rol proll+4-176
            rol proll+4-184
            rol proll+4-192
            rol proll+4-200
            rol proll+4-208
            rol proll+4-216
            rol proll+4-224
            rol proll+4-232
            rol proll+4-240
            rol proll+4-248
            rol proll+4-256
            rol proll+4-264
            rol proll+4-272
            rol proll+4-280
            rol proll+4-288
            rol proll+4-296
            rol proll+4-304
            rol proll+4-312
            clc
            rol proll+5
            rol proll+5-8
            rol proll+5-16
            rol proll+5-24
            rol proll+5-32
            rol proll+5-40
            rol proll+5-48
            rol proll+5-56
            rol proll+5-64
            rol proll+5-72
            rol proll+5-80
            rol proll+5-88
            rol proll+5-96
            rol proll+5-104
            rol proll+5-112
            rol proll+5-120
            rol proll+5-128
            rol proll+5-136
            rol proll+5-144
            rol proll+5-152
            rol proll+5-160
            rol proll+5-168
            rol proll+5-176
            rol proll+5-184
            rol proll+5-192
            rol proll+5-200
            rol proll+5-208
            rol proll+5-216
            rol proll+5-224
            rol proll+5-232
            rol proll+5-240
            rol proll+5-248
            rol proll+5-256
            rol proll+5-264
            rol proll+5-272
            rol proll+5-280
            rol proll+5-288
            rol proll+5-296
            rol proll+5-304
            rol proll+5-312
            clc
            rol proll+6
            rol proll+6-8
            rol proll+6-16
            rol proll+6-24
            rol proll+6-32
            rol proll+6-40
            rol proll+6-48
            rol proll+6-56
            rol proll+6-64
            rol proll+6-72
            rol proll+6-80
            rol proll+6-88
            rol proll+6-96
            rol proll+6-104
            rol proll+6-112
            rol proll+6-120
            rol proll+6-128
            rol proll+6-136
            rol proll+6-144
            rol proll+6-152
            rol proll+6-160
            rol proll+6-168
            rol proll+6-176
            rol proll+6-184
            rol proll+6-192
            rol proll+6-200
            rol proll+6-208
            rol proll+6-216
            rol proll+6-224
            rol proll+6-232
            rol proll+6-240
            rol proll+6-248
            rol proll+6-256
            rol proll+6-264
            rol proll+6-272
            rol proll+6-280
            rol proll+6-288
            rol proll+6-296
            rol proll+6-304
            rol proll+6-312
            clc
            rol proll+7
            rol proll+7-8
            rol proll+7-16
            rol proll+7-24
            rol proll+7-32
            rol proll+7-40
            rol proll+7-48
            rol proll+7-56
            rol proll+7-64
            rol proll+7-72
            rol proll+7-80
            rol proll+7-88
            rol proll+7-96
            rol proll+7-104
            rol proll+7-112
            rol proll+7-120
            rol proll+7-128
            rol proll+7-136
            rol proll+7-144
            rol proll+7-152
            rol proll+7-160
            rol proll+7-168
            rol proll+7-176
            rol proll+7-184
            rol proll+7-192
            rol proll+7-200
            rol proll+7-208
            rol proll+7-216
            rol proll+7-224
            rol proll+7-232
            rol proll+7-240
            rol proll+7-248
            rol proll+7-256
            rol proll+7-264
            rol proll+7-272
            rol proll+7-280
            rol proll+7-288
            rol proll+7-296
            rol proll+7-304
            rol proll+7-312
            rts

//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
// Dane
//@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

//----------------------------------------------------------
            *=$1000 "Music"
            .label music_init =*
            .label music_play =*+3
            .import binary "ode to 64.bin"

//----------------------------------------------------------
            *=$4000 "Logo"
            .import binary "LOGO.prg", 2

            *=$3000 "Fonts"
            .import binary "PROPFONT.prg", 2

            *=$0900 "Text"
            .text "Hello and welcome! ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789. Code: Jacek 'Quasar' Pietruszka.                   "
            .text "                                                                "
            .byte $ff

//dataur:   !byte $81,$02,$03
wsktekst: .word tekst
znak:     .byte $00
spacja:   .byte $00
wskcol:   .byte $07
fclrcol:  .byte $00
dlspacji: .byte $05
