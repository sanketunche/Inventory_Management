
/*****************************************************************************
 * Copyright (C) Kaushik B. Burkule kaushikburkule@gmail.com
 *
 * This program is free software; you can redistribute it and/or modify it
 * under the terms of the GNU Lesser General Public License as published by
 * the Free Software Foundation; either version 2.1 of the License, or
 * (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 * GNU Lesser General Public License for more details.
 *
 * You should have received a copy of the GNU Lesser General Public License
 * along with this program; if not, write to the Free Software Foundation,
 * Inc., 51 Franklin Street, Fifth Floor, Boston MA 02110-1301, USA.
 *****************************************************************************/

/*						Inventory Management System				*/


#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ncurses.h>
#include <unistd.h>
#include <time.h>
#include <errno.h>
#include <limits.h>
#include <float.h>

/* Hexadecimal Codes */
#define KRED  "\x1B[31m"
#define RESET "\033[0m"
#define KGRN  "\x1B[32m"
#define KYEL  "\x1B[33m"
#define KBLU  "\x1B[34m"
#define KMAG  "\x1B[35m"
#define KCYN  "\x1B[36m"
#define KWHT  "\x1B[37m"
#define TRUE 1
#define FALSE 0

/* Data Define For File Edit */
int fEdit;
#define TRUE 1
#define FALSE 0
#define DELAY 30000

void Entry();
void getConfiguration();

/*Data Structure to Store Main Information Permantaly */
static struct struct_stock{						/* Main database structure*/
	char itemcode[8];
  	char itemname[50];
  	float itemrate;
  	float itemqty;
  	int minqty;	                   
}inv_stock;								/*Used for Reorder level, which is the minimum no of stock*/

/* Data Structure for the Items Information Storage */
struct struct_bill{
  	char itemcode[8];
  	char itemname[50];
  	float itemrate;
 	 float itemqty;
  	float itemtot;
}item_bill[100];

char password[8];							// Password Storage

const long int stocksize=sizeof(inv_stock);   
float tot_investment;
int numItems;                                				/*To count the no of items in the stock*/
int button,column,row;                       				/*To allow mouse operations in the application*/
FILE *dbfp,*fp;								/*To perform database file operations on "inv_stock.dat"*/

int main(int argc, char *argv[]){					/* Main Function of The Programm */
	
	//clear the Running 
  	system("clear");
	
	/*while(1) {
    		getmaxyx(stdscr, max_y, max_x);
    		clear();
    		mvprintw(y, x, "o");
    		refresh();
  	}*/

  	float issued_qty;
  	char userchoice,code[8];
 	int choice;
 	int flag,i,itemsold;
  	float getInvestmentInfo(void);
  	FILE *ft;
  	int result;
  	char user[20],pass[20];

	Entry();

  	//fp=fopen("admin.txt","a+");
  		//if(dbfp == NULL){
			//printf(KRED "Someething is going wrong...Please try after some time\n" RESET);
		//}
  		//else{
    			//printf(KBLU "\t\t Usename : \n" RESET);
    			//printf(KBLU "\t\t Password : \n" RESET);
		//}

  	/* Opens & set 'dbfp' globally so that it is accessible from anywhere in the application*/
  	dbfp=fopen("invstoc.txt","a+");
  	if(dbfp == NULL){
		printf(KRED "Someething is going wrong...Please try after some time\n" RESET);
	}
  	else{
    	printf(KWHT "\t\t\n\n\n\n\n\t\t\t Welcome to Inventory Management System .\n" RESET);
    	printf(KBLU "\t\t-------------------------------------------------------------------------------\n" RESET);
    
    	while(1){
    	fEdit=FALSE;
    	numItems=0;
    	rewind(dbfp);
    	while(fread(&inv_stock,stocksize,1,dbfp)==1)
     	++numItems;
   	printf("\n\t Total Items in Stock: %d",numItems);
    	fflush(stdin);
    	ShowMenu();
    	printf("\t Give your choice -> ");
    	scanf("%d",&choice);
    	switch(choice){
	case 1 :
 		getinfo();
		fseek(dbfp,0,SEEK_END);					/*Write the item information into the database*/
		fwrite(&inv_stock,stocksize,1,dbfp);
		printf(KWHT "\t The item has been successfully added. \n" RESET);
		printf(KBLU "\t ------------------------------------- \n" RESET);	
		break;
	
	/* To edit the item information*/		
	case 2 :
		printf("\t Enter Item Code>");
		scanf("%s",&code);
		if(CheckId(code)==0){
			fEdit=TRUE;
			getdata();
			fflush(stdin);
	  		fseek(dbfp,-stocksize,SEEK_CUR);
			/*Write the item information into the database*/
	  		fwrite(&inv_stock,stocksize,1,dbfp);
		}
		else{
	  		printf("\t The item is not available in the database.\n ");
			printf(KBLU "\t ------------------------------------- \n" RESET);
	  		fEdit=FALSE;
	  	}
		break;

	/* To show information about an an Item*/	
	case 3 :
			printf("\t Enter Item Code: ");
			scanf("%s",&code);
			flag=0;
			rewind(dbfp);
			//while(fread(&inv_stock,stocksize,1,dbfp)==1){
	  			//if(strcmp(inv_stock.itemcode,code)==0){
	     				DisplayItemInfo();
	     				flag=1;
	  			//}
			//}
			if(flag==0)
	  		printf("\t The item is not available.\n ");
			printf("\t\t --------------------------\n ");
		break;

	/* To show information about all items in the database*/
	case 4 :
		//If Items are not Available.
		if(numItems==0){
	        	printf("\t\t No items are available. ");
		}
		//Items are avilable shown Here.
		else{
			printf("\t\t Number of Items Available in Stock:  %d",numItems);
			getInvestmentInfo();
			printf("\t\t Total Investment :Rs.%.2f",tot_investment);
			printf("\t\t ----------------------\n");
			fflush(stdin);
	  		rewind(dbfp);
	  		while(fread(&inv_stock,stocksize,1,dbfp)==1);
	    		DisplayItemRecord(inv_stock.itemcode);
		}
		break;
	
	/* To issue Items*/
	case 5 :		
        	itemsold=0;
        	i=0;
		top:
		printf("\t Enter Item Code: ");						//Item Code 
		fflush(stdin);
		scanf("%s",&code);
		rewind(dbfp);
		while(fread(&inv_stock,stocksize,1,dbfp)==1){
	  		if(strcmp(inv_stock.itemcode,code)==0){
	     			issued_qty=IssueItem();
	     			if(issued_qty > 0){
	       				itemsold+=1;
	       				strcpy(item_bill[i].itemcode,inv_stock.itemcode);
	       				strcpy(item_bill[i].itemname,inv_stock.itemname);
	       				item_bill[i].itemqty=issued_qty;
	       				item_bill[i].itemrate=inv_stock.itemrate;
	       				item_bill[i].itemtot=inv_stock.itemrate*issued_qty;
	       				i+=1;
	     			}
			}
		}
		break;

	/* Items to order*/
	case 6 :
		//Number of Items cant be less than Zero or Nagative Number.
		if(numItems<=0){
	  		printf("\t\t No items are available. ");
	  		break;
		}
		printf("\t\t Stock of these items is on the minimum level \n");
		fflush(stdin);
		flag=0;
		fflush(stdin);
		rewind(dbfp);
		//while(fread(&inv_stock,stocksize,1,dbfp)==1){
	  		//if(inv_stock.itemqty <= inv_stock.minqty){
	    			DisplayItemInfo();
	    			flag=1;
	 		//}
		//}
		if(flag==0)
	   	printf("\t\t No item is currently at reorder level. \n");
		printf("\t\t -------------------------------------- \n");
		break;

	//Exit and Close Application Having Store all the Data.	
	case 0 : 
		//Close the all files and exit appliation.
		fclose(dbfp);
		fcloseall();
		printf("\t\t Thanks for Using the application. \n\n\n\n\n\n\n\n\n");
		sleep(1);
		exit(0);
		break;
	 }
      }
   }
}

/*Display Menu & Skins that the user will see*/
ShowMenu(){

    //Choice Codes
    printf(KWHT "\n\t 1: Add an Item \n");
    printf("\t 2: Edit Item Information \n");
    printf("\t 3: Show Item Information \n");
    printf("\t 4: View Stock Report \n");
    printf("\t 5: Issue Items from Stock \n");
    printf("\t 6: View Items to be Ordered \n");
    printf("\t 0: Close the application \n" RESET);
    return;

}

/*Returns 0 if the id already exists in the database, else returns 1*/
int CheckId(char item[8]){
		//printf("\t Terminated Here!");
  		rewind(dbfp);
  		while(fread(&inv_stock,stocksize,1,dbfp)==1)
    		if(strcmp(inv_stock.itemcode,item)==0)
      		return(0);
  	return(1);
}

/*Displays an Item information*/
DisplayItemInfo(){

  	printf("\t Item Code: %s \t",inv_stock.itemcode);
  	printf("Name of the Item: %s \n",inv_stock.itemname);
  	printf("\t Price of each unit: %.2f \t",inv_stock.itemrate);
 	 printf("Quantity in Stock: %.4f \n",inv_stock.itemqty);
  	printf("\t Reorder Level: %d \n",inv_stock.minqty);
  	fflush(stdin);
  return;

}


/* Calculates the total investment amount for the stock available*/
float getInvestmentInfo(void){

   tot_investment=0;
   rewind(dbfp);
   while(fread(&inv_stock,stocksize,1,dbfp)==1)
     tot_investment+=(inv_stock.itemrate*inv_stock.itemqty);
    return tot_investment;

}

/* Function For the Record Item Display */
DisplayItemRecord(char idno[8]){
 
	rewind(dbfp);
 	while(fread(&inv_stock,stocksize,1,dbfp)==1)
   	if(strcmp(idno,inv_stock.itemcode)==0)
     	DisplayItemInfo();
 	return;
}

/*Wait for response from the user & returns choice*/
IssueItem(){
	
	/*This function will return 0 if an item cannot issued, else issues the item*/
  	float issueqnty;
  	DisplayItemInfo();
  	printf("\t Enter Quantity: ");
  	fflush(stdin);
  	scanf("%f",&issueqnty);
	
	/*If the stock of the item is greater than minimum stock*/
  	if((inv_stock.itemqty - issueqnty) >= inv_stock.minqty){
    		printf("\t %.4f  Item(s) issued.",issueqnty);
    		printf("\t You should pay RS. %.2f",issueqnty*inv_stock.itemrate);
    		inv_stock.itemqty-=issueqnty;       
    		fseek(dbfp,-stocksize,SEEK_CUR);				/*Write the item information into the database*/
    		fwrite(&inv_stock,stocksize,1,dbfp);
    		return issueqnty;
  	}

	/* If the stock of the item is less than minimum stock.ie Reorder level*/
  	else{
    		printf("\t Insufficient quantity in stock.");
    		printf("\t ONLY %.4f pieces of the Item can be issued.",inv_stock.itemqty-inv_stock.minqty);
    	return 0;
  	}
}

/* Items Information Shown Here **/
getinfo(){

	fcloseall();
	printf("\t\t Enter The Item code -> ");
	scanf("%s",&inv_stock.itemcode);  
	printf("\t\t Enter The Item Name -> ");
	scanf("%s",&inv_stock.itemname);
 	printf("\t\t Enter The Item Rate -> ");
	scanf("%f",&inv_stock.itemrate);
 	printf("\t\t Enter The Item Qty -> ");
	scanf("%f",&inv_stock.itemqty);
 	printf("\t\t Enter The Reorder Level -> ");
	scanf("%d",&inv_stock.minqty);
}

/*Reads a valid id and its information,returns 0 if id already exists*/
getdata(){
	
	/*int x = 0, y = 0;
  	int max_y = 0, max_x = 0;
  	int next_x = 0;
  	int direction = 1;
 	initscr();
  	noecho();
  	curs_set(FALSE);
  	// Global var `stdscr` is created by the call to `initscr()`
  	getmaxyx(stdscr, max_y, max_x);
  	while(1) {
    		clear();
    		mvprintw(y, x, "o");
    		refresh();
    		usleep(DELAY);
    		next_x = x + direction;
    		if (next_x >= max_x || next_x < 0) {
      			direction*= -1;
    		} 
		else {
      		x+= direction;
    		}
  	}
  	endwin();*/

  	char tmp[8];
  	float tst;
  	printf("\n\t\t Enter Item Code: ");
  	scanf("%s",&tmp);
  	if(CheckId(tmp)==0 && fEdit == FALSE){
    		printf("The id already exists. ");
    		return 0;
  	}
  	strcpy(inv_stock.itemcode,tmp);
  	printf("Name of the Item: ");
  	scanf("%s",inv_stock.itemname);
  	printf("Price of Each Unit: ");
  	scanf("%f",&inv_stock.itemrate);
  	printf("Quantity:  ");
  	scanf("%f",&inv_stock.itemqty);
  	printf("Reorder Level: ");
  	scanf("%d",&inv_stock.minqty);
  	return 1;
}

/* When Project Starts First Authentication Start Here By Password and Username */
void Entry(){
	char uname[20], password[20], uname1[20], password1[20];
	FILE *fp;
	int choice, i, err;
	//display();
	system("clear");
	printf("\n\n\n");
	system("setterm -bold on");
	printf(KYEL "\n\n\t\t\t\t\tWelcome to the Inventory Management System\n\n" RESET);
	system("setterm -bold off");
	for(i = 0; i < 140; i++){
		printf("-");	
	}
	printf("\n\n\t\t\t\t\tPlease LogIn to Continue\n\t\t\t\t\tUsername : ");
	scanf("%s",uname);
	printf("\t\t\t\t\tPassword : ");
	scanf("%s",password);
	printf("\n\n");
	fp = fopen("config.txt","r");
	if(fp == NULL){
		printf("...Something is Going Wrong ..PLease try after some time\n");
	}
	else{
		fscanf(fp, "%s\t", uname1);
		fscanf(fp, "%s", password1);
		if(strcmp(uname,uname1) == 0 && strcmp(password,password1) == 0){
			system("clear");
			printf(KBLU "\n Logged In...\n\n" RESET);
			//printf("\t\t\t\t\t____________________\n\n");
			fclose(fp);
			//selectChoice();
		}
		else{
			fclose(fp);
			system("clear");
			printf("Sorry threre is Some Problem\n ");
			sleep(1);
			exit(0);
		}
	}
}

/* Creates a backup file "Bakckup" of "inv_stock.dat"*/
BackupDatabase(void){
  	FILE *fback;
  	fback=fopen("d:/Backup.dat","w");
  	rewind(dbfp);
  	while(fread(&inv_stock,stocksize,1,dbfp)==1)
    	fwrite(&inv_stock,stocksize,1,fback);
  	fclose(fback);
  	return;
}

/* Gets the last Configuration Saved By the Useror Application */
void getConfiguration(){

  	FILE *flast;
  	flast=fopen("lastcfg","r+");
  	if(flast==NULL){
  	}
  	rewind(flast);

  	/*Reads the first record.*/
    	fclose(flast);
}
