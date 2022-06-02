# kursovaya

#include <graphics.h>
#include <conio.h>
#include <stdio.h>
#include <stdint.h>
struct element
{
    int number;
    char symbol[4];
    char name[32];
    int row;
    int column;
    int color;
    char path[30];
    char info_elements[40];
};

struct data
{
    char name [30];
    int color;
};
struct text
{
    char str[40];
};

int read_file(element *p,char *filename,data *p1,char *filename1);
void process_mouse_click(element *p,data *p1,text *p2);
void draw_elements(element *p,data *p1);
void discribe(int x1,int y1,int x2,int y2,int xz,int yz,data *p1,int i);
int read_info_elements(text *p2,char *s1);
void draw_element_description(int x0,int y0,char *s,element *p,data *p1,text *p2);
int draw_bmp(int x0,int y0, char *s);
void outtexts(text *p2);
void titul();


int main(void)
{
    setlocale(LC_CTYPE,"Russian");
    int driver, mode, rez;
    driver = IBM8514; /* автоопределение */
    mode = 1;
    initgraph(&driver, &mode, " " );

    rez=graphresult() ;
    if(rez != grOk)
    {
        printf("\n Error graph modeи") ;
        return(1) ;
    }

    struct element  elements[118];
    struct element *p=elements;

    struct data datas[10];
    struct data *p1=datas;

    struct text texts[118];
    struct text *p2=texts;

    int x,y;
    char ch;
    //650,500,800,580
    titul();

    do
    {
        getmouseclick(WM_LBUTTONDOWN,x,y);
       if((x>650)&&(y>500)&&(x<800)&&(y<580))
       {

           read_file(p,"Original.txt",p1,"Data.txt");
           draw_elements(p,p1);
           process_mouse_click(p,p1,p2);

       }

    }while ( ch!= 'e');
    getchar();
    return 0;
}
int read_file(element *p,char *filename,data *p1,char *filename1)
{
    int i;
    FILE *file;
    FILE *file1;
    FILE *file2;
    if((file=fopen(filename,"r"))== NULL)
    {
        printf("Error occured while opening file\n");
        return 1;
    }
    else
    {
        i=0;
        while(fscanf(file, "%d %s %s %d %d %d %s %s\n",&(*(p+i)).number,(p+i)->symbol,(p+i)->name,&(*(p+i)).row,&(*(p+i)).column,&(*(p+i)).color,(p+i)->path,(p+i)->info_elements)!=EOF)
        {
            printf("%3d %4s %-20s %-3d %-3d %-3d %-15s %-15s\n",(*(p+i)).number,(p+i)->symbol,(p+i)->name,(*(p+i)).row,(*(p+i)).column,(*(p+i)).color,(p+i)->path,(p+i)->info_elements);
            i++;
        }
    }
    fclose(file);

    if((file1=fopen(filename1,"r"))== NULL)
    {
        printf("Error occured while opening file\n");
        return 1;
    }
    else
    {
        i=0;
        while(fscanf(file1, "%s %d\n",(p1+i)->name,&(*(p1+i)).color)!=EOF)
        {
            printf("%-25s %d\n",(p1+i)->name,(*(p1+i)).color);
            i++;
        }
    }
    fclose(file1);



}
void process_mouse_click(element *p,data *p1,text *p2)
{
    draw_elements(p,p1);
    int x,y,i;
    int x1,x2,y1,y2;
    do
    {
        getmouseclick(WM_LBUTTONDOWN, x,y);

        for(i=0; i<118; i++)
        {
            x1=30+(50 * ((*(p+i)).row));
            y1=50+(60 * ((*(p+i)).column));
            x2=80+(50 * ((*(p+i)).row));
            y2=110+(60* ((*(p+i)).column));

            if((x>x1)&&(y>y1)&&(x<x2)&&(y<y2))
            {

                read_info_elements(p2,(p+i)->info_elements);
                draw_element_description(620,200,(p+i)->path,p,p1,p2);
                break;
            }

        }
    }
    while(true);
}
void draw_elements(element *p,data *p1)
{setfillstyle(1,15);
    bar(0,0,1024,769);
    readimagefile("sosud.jpg",30,480,220,650);
    int i=0;
    int x1,x2,y1,y2,x,y;
    char st[3];
    setcolor(0);
    for(i=0; i<118; i++)
    {

        x1=30+(50 * ((*(p+i)).row));
        y1=50+(60 * ((*(p+i)).column));
        x2=80+(50 * ((*(p+i)).row));
        y2=110+(60* ((*(p+i)).column));

        setfillstyle(1,(*(p+i)).color);
        bar(x1,y1,x2,y2);
        rectangle(x1,y1,x2,y2);

        setbkcolor((*(p+i)).color);
        sprintf(st,"%d",(*(p+i)).number);
        outtextxy(x1+17,y1+2,st);

        settextstyle(4,0,1);
        outtextxy(x1+4,y1+20,(p+i)->symbol);
        settextstyle(2,0,5);
        outtextxy(x1+1,y1+40,(p+i)->name);

    }
    for(i=0; i<10; i++)
    {
        discribe(180,10+(20*i),190,20+(20*i),195,5+(20*i),p1,i);
    }


}
void discribe(int x1,int y1,int x2,int y2,int xz,int yz,data *p1,int i)
{

    setfillstyle(1,(*(p1+i)).color);
    setbkcolor(15);
    bar(x1,y1,x2,y2);
    setcolor(0);
    settextstyle(6,0,2);
    outtextxy(xz,yz,(p1+i)->name);
    
}
int read_info_elements(text *p2,char *s1)
{

    int i;
    FILE *file;
    if((file=fopen(s1,"r"))== NULL)
    {
        printf("Error occured while opening file\n");
        return 1;
    }
    else
    {
        i=0;
        while(fscanf(file, "%s\n",(p2+i)->str)!=EOF)
        {
            printf("%s\n",(p2+i)->str);
            i++;
        }
    }
    fclose(file);
    
}
void draw_element_description(int x0,int y0 ,char *s,element *p,data *p1,text *p2)
{
    int x,y;
    setfillstyle(1,15);
    bar(0,0,1024,769);
    draw_bmp(x0,y0,s);
    delay(1000);
    outtexts(p2);


    setfillstyle(1,4);
    bar(5,5,25,45);
    do
    {
        getmouseclick(WM_LBUTTONDOWN, x,y);
        if((x>5)&&(y>5)&&(x<25)&&(y<45))
        {
            process_mouse_click(p,p1,p2);
            break;
        }

    }
    while(true);


}
int draw_bmp(int x0,int y0, char *s)
{
    //таблица преобразования кодировки цвета
    // unsigned char color[16]= { 0,4,2,6,9,5,3,8,7,12,10,14,1,13,11,15};
    unsigned char color[16]= { 0,4,2,6,1,5,3,8,7,12,10,14,9,13,11,15};

    FILE *f;                 //

    BITMAPFILEHEADER infoFH;
    BITMAPINFOHEADER info;



    int x,y;                 // координаты пискеля
    unsigned char b;         // байт, прочитанный из файла
    unsigned char bh;        // старший полубайт
    unsigned char bl;        // младший полубайт

    int  nb;                 // количество байт, (кратное 4) соотвествующее строке
    int np;                  // количество выделенных пискселей
    int i,j;

    if ((f=fopen(s, "rb"))==NULL)
    {
        setcolor(4);
        outtextxy(0,0,"File absent");
        printf("\n\n %s",s);
        return -1;
    }

    fread(&infoFH, sizeof(BITMAPFILEHEADER),1,f);
    if (infoFH.bfType !=0x4D42)
    {
        setcolor(4);
        outtextxy(0,0,"File is not bmp16");

        return -2;
    }
    fread(&info, sizeof(BITMAPINFOHEADER),1,f);

    fseek(f, infoFH.bfOffBits, SEEK_SET);

    x=x0;
    y=y0+info.biHeight;

    nb=(info.biWidth / 8)*4;
    if ((info.biWidth / 8) !=0)
        nb +=4;


    for(i=0; i < info.biHeight; i++)
    {
        np=0;
        for(j=0; j < nb; j++)
        {
            b=fgetc(f);
            if(np < info.biWidth)
            {
                bh = b >> 4;
                putpixel(x,y,color[bh]);
                x++;
                np++;
            }
            if (np < info.biWidth)
            {
                bl = b & 0xF;
                putpixel(x,y,color[bl]);
                x++;
                np++;
            }
        }
        x=x0;
        y--;
    }
    fclose(f);
    return info.biHeight;

}
void outtexts(text *p2)
{
    setcolor(0);
    int j=0,i=0;
    for(i=0,j=100; i<10,j<460; i++)
    {
         outtextxy(15,50+j,(*(p2+i)).str);
        j+=25;
    }

}
void titul()
{

    readimagefile("fon.jpg",0,0,1024,769);
    settextstyle(6,0,1);
    setbkcolor(15);
    setcolor(0);
    outtextxy(240,90,"KYRGYZ RUSSIAN SLAVIC UNIVERSITY");
    outtextxy(240,120,"SOFTWARE ENGINEERING DEPARTMENT");
    outtextxy(400,180,"KURSOVAYA");
    outtextxy(280,230,"PERIODIC TABLE OF D.I.MENDELEEV");
    readimagefile("mendeleev1.jpg",360,250,630,650);
    outtextxy(600,600,"Student: Orozmamatova Jibek");
    outtextxy(600,630,"Teacher: Belogubova M.M");
    readimagefile("trynow.jpg",650,500,800,580);
    
}



