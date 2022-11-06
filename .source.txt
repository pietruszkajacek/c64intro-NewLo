
BasicUpstart2(start)

.const pickolor     = $5f40
.const ekrancol     = $6800
.const fonts        = $3000

.var proll          = $0d38 // $5f38

.var ptrchar  = $fb //(2)
.var ptrcol   = $fd //(2)

            *=$2500 "Main Program"
start:      sei
            lda #$3b
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
cpycol:	    lda pickolor + 0*$100,x
            sta ekrancol + 0*$100,x
            lda pickolor + 1*$100,x
            sta ekrancol + 1*$100,x
            lda pickolor + 2*$100,x
            sta ekrancol + 2*$100,x
            lda pickolor + 3*$100,x
            sta ekrancol + 3*$100,x
            inx
            bne cpycol
            lda #$0c
            sta $d020
            jsr copyline

            lda #$00
            jsr music_init

            jsr clrscroll

petla:      lda #$f0
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

.label rolluj = *
            .for(var x=0; x<8; x++) {
                clc
                .for(var y=0; y<320; y=y+8) {
                    rol proll+x-y
                }
            }
            rts

dataur:     .byte $81,$02,$03,$00
credits:    .text "code, logo: quasar/cosmic top secret group."
wsktekst:   .word tekst
znak:       .byte $00
spacja:     .byte $00
wskcol:     .byte $07
fclrcol:    .byte $00
dlspacji:   .byte $05

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
            .label tekst =*
            .text "Hello and welcome! ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789. Code, logo by Jacek 'Quasar' Pietruszka."
            .text "                                                                                                                   "
            .byte $ff


