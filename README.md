#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<ctype.h>

void warning(void);
void error(void);
void Vigenere (void);
void Cesar (void);
char bidule[512] /*buffer de saisie*/;
char *nomcle /*nom du fichier ou est enregistree la cle*/, *nomcrypt/*nom du fichier ou est enregistre le resultat*/;
char *buffer /*string de depart*/, *falsemotcle /*cle pour copie dans fichier */, *motcle /*cle réellement utilisée*/,*crypto /*resultat*/;
int i,j;
int un=0, deux=0;
int choix, nombre;
FILE *cle=NULL,*crypt=NULL;

int main(int argc,char *argv[]) /* ex :  fhcrypto  Salut zorro  2 crypte.txt cle.txt     donne :  buffer = salut ,cle = salut,choix =2 (chiffrement de vigenère), nomcrypt = crypte.txt ,nomcle = cle.txt*/
{                               /* ou :  fhcrypto  Salut 3   1 crypte.txt cle.txt        donne :  buffer = salut ,cle = 3,choix =2 (chiffrement de césar), nomcrypt = crypte.txt ,nomcle = cle.txt*/

    if(argc!=1 && argc !=6)
    {
        printf("Nombre d'arguments errone !\nFin du programme.");
        exit(1);
    }

    if(argc==6)
        strcpy(bidule,argv[1]);

    else
    {
        printf("Saisie de la string a crypter : \n");
        gets(bidule);
    }

    if(!(buffer = (char *) malloc (strlen(bidule))))
        warning();
    strcpy(buffer,bidule);

    if(!(crypto = (char *) malloc (strlen(bidule))))
        warning();

    if(argc==6)
        choix=atoi(argv[3]);
    else
    {
    printf("Choisissez votre algorithme :\n\n\t_1 Chiffrement de Cesar.\n\n\t_2 Algorithme de Vigenere.\n");
    scanf("%d", &choix);
    getchar();
    }

    switch(choix)
    {
        default : while(isdigit(choix) && choix!=1 && choix !=2)
                  {
                      printf("Erreur de choix (refaites-le :\n");
                      scanf("%d", &choix);
                      getchar();
                  }
                  break;

        case 1 :
                 if(argc==6)
                 {
                 nombre = atoi(argv[2]);
                 un = 1;
                 }
                 Cesar();
                 break;
        case 2 : if(argc==6)
                 {
                 strcpy(bidule,argv[2]);
                 deux=1;
                 }
                 Vigenere();
    }

    printf("\n\n\nBuffer crypte = \n%s", crypto);

    if(argc==6)
    {
        if(!(nomcrypt = (char *) malloc (strlen(argv[4]))))
            warning();

        strcpy(nomcrypt, argv[4]);
    }
    else
    {
        if(!(nomcrypt = (char *) malloc (256)))
            warning();

        printf("\n\n\nLe resultat du cryptage va etre enregistre dans un fichier.\nDonnez le nom du fichier (et eventuellement un chemin) :");
        gets(nomcrypt);
    }

    crypt = fopen (nomcrypt, "w+");

    if(!crypt)
        error();

    fprintf(crypt, "Resultat du cryptage. Le resultat est = \n%s",crypto);
    fclose(crypt);
    printf("\n\nResultat enregistre dans %s\n", nomcrypt);

    if(argc==6)
    {
        if(!(nomcle = (char *) malloc (strlen(argv[5]))))
            warning();

        strcpy(nomcle, argv[5]);
    }
    else
    {
        if(!(nomcle = (char *) malloc (256)))
            warning();

        printf("\n\nLa cle (ou le nombre) va etre enregistre(e) dans un fichier.\nDonnez le nom du fichier (et eventuellement un chemin) :");
        gets(nomcle);

    }

    cle = fopen (nomcle, "w+");

    if(!cle)
        error();

    switch(choix)
    {
    case 1 : fprintf(cle, "Numero de l'algorithme. Le numero est = \n%i\nNombre de decryptage. Le nombre est = \n%i",choix,nombre);
             break;

    case 2:  fprintf(cle, "Numero de l'algorithme. Le numero est = \n%i\nCle de decryptage. Le mot cle est = \n%s", choix,falsemotcle);
             break;
    }
    fclose(cle);
    printf("Cle enregistree dans %s\n", nomcle);

    printf("Fin du programme");

    if(argc==1)
       getchar();

    free(buffer);
    free(crypto);
    return 0;

}

void warning(void)
{
    printf("Allocation memoire impossible !!\nFin du programme.");
    getchar();
    exit(1);
}
void error(void)
{
    printf("Erreur d'ouverture du fichier.\nFin du programme.");
    getchar();
    exit(1);
}

void Vigenere (void)
{

    if(!deux)
    {
        printf("Saisie de la cle : \n");
        gets(bidule);
    }
    if(!(falsemotcle = (char *) malloc (strlen(bidule))))
        warning();
    strcpy(falsemotcle, bidule);

    if(!(motcle = (char *) malloc (strlen(buffer))))
        warning();

    for(i=0,j=0;strlen(motcle) <strlen(buffer);motcle[j] = bidule[i], j++,i++)
        if(i == strlen(bidule) )
            i=0;

    for(i=0;buffer[i]!='\0';crypto[i] = (buffer[i]+motcle[i])%256,i++)
        ;
}

void Cesar (void)
{

    if(!un)
    {
        printf("Saisie du nombre de decalage : \n");
        scanf("%d", &nombre);
        getchar();
    }
    for(i=0;buffer[i]!='\0';crypto[i] = (buffer[i]+nombre)%256,i++)
        ;

}
