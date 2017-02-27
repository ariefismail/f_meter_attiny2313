#include <tiny2313a.h>
#include <stdio.h>
#include <delay.h>
#include <math.h>
#define ss PORTB.2
#define USI_MOSI (1 << 5)
#define USI_MISO (1 << 6)
#define USI_SCK (1 << 7)
#define	USI_CLK_L ((1<<USIWM0)|(1<<USITC))
#define	USI_CLK_H ((1<<USIWM0)|(1<<USITC)|(1<<USICLK))

unsigned int overflow;
unsigned long int f;
unsigned char pulsa;
char tampil[8];
char i,j;
void data(unsigned char address,unsigned char data);

interrupt [TIM0_OVF] void timer0_ovf_isr(void){
overflow++;
//TCNT0=0;
}

// Timer1 output compare A interrupt service routine
interrupt [TIM1_COMPA] void timer1_compa_isr(void)
{
TCCR0B=0x00;
TCCR1B=0x00;
pulsa=TCNT0;
f=(unsigned long int)overflow*256+pulsa;
//f*=16;
//f=(f+500)/1000*1000;

overflow=0;
TCNT1=0;
TCNT0=0;
tampil[7]= f/10000000;
tampil[6]=(f/1000000)%10;
tampil[5]=(f/100000)%10;
tampil[4]=(f/10000)%10;
tampil[3]=(f/1000)%10;
tampil[2]=(f/100)%10;
tampil[1]=(f/10)%10;
tampil[0]= f%10;

for(i=7;i>=1;i--){
    if(tampil[i]>0){
        for(j=7;j>i;j--)tampil[j]=15;
    break;
    }
    else if(i==1){
        for(j=7;j>0;j--)tampil[j]=15;
    }
}

if(tampil[3]!=15)tampil[3]|=0b10000000;
if(tampil[6]!=15)tampil[6]|=0b10000000;
for(i=0;i<8;i++){
    data(i+1,tampil[i]);
}

TCCR1B=0x0F;
TCCR0B=0x07;
}


void main(void){
#pragma optsize-
CLKPR=0x80;
CLKPR=0x00;
#ifdef _OPTIMIZE_SIZE_
#pragma optsize+
#endif

DDRB  = 0xff;

// Timer/Counter 0 initialization
// Clock source: T0 pin Rising Edge
// Mode: Normal top=0xFF
// OC0A output: Disconnected
// OC0B output: Disconnected
TCCR0B=0x07;


TCCR1B=0x0F;
OCR1AH=0x7f;
OCR1AL=0xff;

// Timer(s)/Counter(s) Interrupt(s) initialization
TIMSK=0x42;

ss=1;

data(12,1);
data(15,0);
data(10,8);
data(11,7);
data(9,255);
data(1,8);
data(2,7);
data(3,6);
data(4,5);
data(5,4);
data(6,3);
data(7,2);
data(8,1);
delay_ms(1000);
ACSR=0x80;
DDRD.6=1;
#asm("sei")
while (1)
      {
      PORTD.6=1;
      PORTD.6=0;
      }
}

void data(unsigned char address,unsigned char data){
ss=0;
	USIDR = address;
	USISR = (1<<USIOIF);
	do {
		USICR = (1<<USIWM0)|(1<<USICS1)|(1<<USICLK)|(1<<USITC);
	} while ((USISR & (1<<USIOIF)) == 0);
    USIDR = data;
	USISR = (1<<USIOIF);
	do {
		USICR = (1<<USIWM0)|(1<<USICS1)|(1<<USICLK)|(1<<USITC);
	} while ((USISR & (1<<USIOIF)) == 0);
ss=1;
}
