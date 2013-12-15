#include <iostream>
#include "red_pok.h"
//#include "red_polja.h"
#include <ctime>
#include <stdlib.h>
#include <string.h>

using namespace std;
int ukupno_pacijenata=0,glavni_red_brojac,pomocni_red_brojac,N;

jedan_covjek*pom2;
queue*red=new queue;

char prioritet[5][25]={"hitni slucajevi","invalidi","trudnice","djeca mlada od 6 godina","ostali pacijenti"};
char vrsta_usluge[4][15]={"pregled","previjanje","recepti","uputnice"};
char vrsta_ordinacije[5][25]={"obiteljska medicina","oftalmologija","dermatovenerologija","ginekologija","urologija"};
jedan_covjek*pet;

/* Datum se unosi u obliku GGGGMMDD, spol se unosi u obliku Z ili M

*/
void generiraj_brojeve(int i){
	srand(time(0));
	pet[i].ai=rand()%251+50;
    pet[i].bi=rand()%251+50;
    pet[i].ci=rand()%5+1;
    pet[i].di=rand()%4+1;
    pet[i].ei=rand()%5+1;
}

bool provjeri_nemoguce_slucajeve(int i){
	if(pet[i].ci==3&&pet[i].ei==5)return false;
    else if(pet[i].ci==1&&pet[i].ei==1)return false;
    return true;
}


void pacijenti() {
    cout<<"N = ";
    cin>>N;
    ukupno_pacijenata=N*3;
    pet=new jedan_covjek[N];
    for(int i=0; i<N; i++) {
        jedan_covjek*pacijent=new jedan_covjek;
		generiraj_brojeve(i);
        if(!provjeri_nemoguce_slucajeve)i--;
    }
}

void ispis(jedan_covjek*pacijent){
	cout<<"Podaci o pacijentu\n";
	cout<<"----------------------------\n";
	cout<<"Ime: "<<pacijent->ime<<endl;
	cout<<"Prezime: "<<pacijent->prezime<<endl;
	cout<<"Datum rodenja: "<<pacijent->datum<<endl;
	cout<<"Spol: "<<pacijent->spol<<endl;
}

void unesi_podatke_o_pacijentu(jedan_covjek*pacijent){
		cout<<"Ime: ";
		cin>>pacijent->ime;
		cout<<"Prezime: ";
		cin>>pacijent->prezime;
		cout<<"Datum rodenja: ";
		cin>>pacijent->datum;
		cout<<"Spol: ";
		cin>>pacijent->spol;
}


void dodaj(){
	jedan_covjek*pacijenti=new jedan_covjek[N*3];
	int br=0;
	for(int i=0;i<N;i++){
		cout<<"Upisite tri pacijenta koji ima slijedece karakteristike: \n";
		cout<<"=========================================================\n";
		
		cout<<"Prioritet= "<<prioritet[(pet[i].ci)-1]<<endl;
		cout<<"Vrsta usluge= "<<vrsta_usluge[(pet[i].di)-1]<<endl;
		cout<<"Vrsta ordinacije: "<<vrsta_ordinacije[(pet[i].ei)-1]<<endl;
		cout<<"=========================================================\n";
		for(int j=0;j<3;j++){
			cout<<endl;
			cout<<"Unesite podatke za pacijenta\n";
			cout<<"--------------------------------------------------------\n";
			jedan_covjek*pacijent=new jedan_covjek;
			memcpy(pacijent,&pet[i],sizeof(jedan_covjek));
			
			unesi_podatke_o_pacijentu(pacijent);
			
		    enQueue(red,pacijent);
		    
		    memcpy(&pacijenti[br],pacijent,sizeof(jedan_covjek));
		    br++;
		}
	}
	cout<<"Ispis POSEBNIH pacijenata\n";
	cout<<"====================================\n";
	for(int i=0;i<N*3;i++)
		if((pacijenti[i].spol=="Z")&&(pacijenti[i].ei==2)&&(pacijenti[i].datum<19881201))
			ispis(&pacijenti[i]);
	system("pause");
}

void pomocni(){
	cout<<"Ispis pomocnog reda: \n";
	cout<<"=========================\n";
	pom2=new jedan_covjek[ukupno_pacijenata];      
	glavni_red_brojac=0;
	while(!empty(red)){
		jedan_covjek*pacijent=new jedan_covjek;
		pacijent=frontQueue(red);	
		deQueue(red);
		if(pacijent->datum<19631201&&pacijent->ci==2&&pacijent->ei==1&&pacijent->di==2){//3. korak
			ispis(pacijent);
			ukupno_pacijenata--;
		}
		else{
			memcpy(&pom2[glavni_red_brojac],pacijent,sizeof(jedan_covjek));
			glavni_red_brojac++;
			}
	}
	cout<<"\nISPIS GLAVNOG REDA\n";
	cout<<"===================================\n";
	red=new queue;
	init(red);
	for(int i=0;i<glavni_red_brojac;i++){
		jedan_covjek*pacijent=new jedan_covjek;
		memcpy(pacijent,&pom2[i],sizeof(jedan_covjek));
		ispis(&pom2[i]);
		enQueue(red,pacijent);
	}
	system("pause");
}

void rekurzija(int posto,int trenutni_broj_izbacenih){  
	if(empty(red)){
		cout<<"\nStanje na pomocnom redu\n";
		cout<<"=============================\n";
		return;
	}
	jedan_covjek*pomocni=new jedan_covjek;
	if(trenutni_broj_izbacenih<posto){
		pomocni=frontQueue(red);
		ispis(pomocni);
		deQueue(red);
		trenutni_broj_izbacenih++;
		rekurzija(posto,trenutni_broj_izbacenih);
	}
	else{
		pomocni=frontQueue(red);
		trenutni_broj_izbacenih++;
		deQueue(red);
		rekurzija(posto,trenutni_broj_izbacenih);
	}
	if(trenutni_broj_izbacenih>posto){
		ispis(pomocni);
		trenutni_broj_izbacenih--;
	}
	else
		enQueue(red,pomocni);
}

int main()
{
    init(red);
    int izbor;
    do{
        cout<<"1. Generiranje brojeva"<<endl;
        cout<<"2. Dodavanje pacijenata u red"<<endl;
        cout<<"3. Brisanje odredenih pacijenata iz reda"<<endl;
        cout<<"4. Brzi red"<<endl;
        cin>>izbor;
        system("cls");
        if(izbor==1)pacijenti();
        else if(izbor==2)dodaj();
        else if(izbor==3)pomocni();
        else if(izbor==4){
        	int posto=0.7*ukupno_pacijenata;
         	cout<<"Stanje na glavnom redu: \n";
         	cout<<"========================\n";
        	rekurzija(posto,0);
        }
    }
    while(izbor!=9);
    return 0;
}

