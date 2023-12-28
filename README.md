# verbose-eureka
#include <stdio.h>
#include <string.h>

struct CAR {
    long immatriculation;
    char état[50];
    char location[50];
    char description[100];
    float prix;
};
struct LOCATION {
    int ID;
    int immatriculation;
    char client[50];
    int periode;
    float tarif;
    char réclamation[100];
};
//les services de notre agence de location
    void louer(struct CAR cars[], int numCars, struct LOCATION louée[], int *numloc);
    void afficher (struct CAR cars[], int numCars);
    void supprimer (struct CAR cars[], int *numCars);
    void modifier(struct CAR cars[], int numCars);
    void historique (struct LOCATION louée[], int numloc);
    void reclamation(struct CAR cars[], int numCars, struct LOCATION louée[], int *numloc);
void ajout (struct CAR cars[], int *numCars);

int main(){
    struct CAR cars[50]; 
    struct LOCATION louée[100];  
    int numCars = 0;
    int numloc = 0;
     int choisir;
    // Les voitures disponibles sont
    FILE *file = fopen("car_infos.txt", "rb");

    if (file != NULL) {
        fread(&numCars, sizeof(int), 1, file);
        fread(cars, sizeof(struct CAR), numCars, file);
        fread(&numloc, sizeof(int), 1, file);
        fread(louée, sizeof(struct LOCATION), numloc, file);
        fclose(file);
    }else{
        printf("ce fichier est indisponible.\n");
    }
    if (file != NULL) {
    file = fopen("car_infos.txt", "wb"); 
    fwrite(&numCars, sizeof(int), 1, file);
    fwrite(cars, sizeof(struct CAR), numCars, file);
    fwrite(&numloc, sizeof(int), 1, file);
    fwrite(louée, sizeof(struct LOCATION), numloc, file);
    fclose(file);
}
    do {
        // menu
        printf("\n MENU DE LOCATION:\n");
        printf("1. LOUER UNE VOITURE\n");
        printf("2. DISPONIBILITE\n");
        printf("3. MODIFICATION DES INFORMATIONS\n");
        printf("4. HISTORIQUE\n");
        printf("5. RECLAMATION\n");
        printf("6.AJOUTER UNE NOUVELLE VOITURE\n");
        printf("7. SUPPRIMER UNE VOITURE\n");
        printf("8. SORTIR\n");
        
        //choix du services
        printf("Entrer votre choix (1-8): ");
        scanf("%d", &choisir);
        
        //donner le service
        switch (choisir) {
            case 1:
                louer(cars, numCars, louée, &numloc);
                break;
            case 2:
                afficher(cars, numCars);
                break;
            case 3:
                modifier(cars, numCars);
                break;
            case 4:
                historique(louée, numloc);
                break;
            case 5:
                reclamation(cars, numCars, louée, &numloc);
                break;
            case 6:
                ajout(cars, &numCars);
                break;
            case 7:
                supprimer(cars, &numCars);
                break;
            case 8:
                printf("Merci pour votre visite ,soyez de retour!\n");
                break;
            default:
                printf("vous devez entrer un nombre entre 1 et 8.\n");
        }
        }while (choisir != 8); // Continuer jusqu'à avoir un chiffre entre 1 et 8
    if (file != NULL) {
        // écrire les informations de la voiture
        fwrite(&numCars, sizeof(int), 1, file);
        fwrite(cars, sizeof(struct CAR), numCars, file);
        // écrire vos détails
        fwrite(&numloc, sizeof(int), 1, file);
        fwrite(louée, sizeof(struct LOCATION), numloc, file);
        fclose(file);
    }
    return 0;
}
//pour louer une voiture : 
    void louer(struct CAR cars[], int numCars, struct LOCATION louée[], int *numloc) {
    printf("entrer l'ID du voiture que vous vouler louer: ");
    int carID;
    scanf("%d", &carID);

    if (carID >= 0 && carID < numCars) {
        if (strcmp(cars[carID].état, "disponible") == 0) {
            struct LOCATION newloc;
            newloc.ID = *numloc;
            newloc.immatriculation = cars[carID].immatriculation;
            printf("Entrer le nom du client: ");
            scanf(" %s", newloc.client);
            printf("Enter la période: ");
            scanf("%d", &newloc.periode);
            newloc.tarif = cars[carID].prix * newloc.periode;

            // mise à jour du status
            strcpy(cars[carID].état, "louée");
            strcpy(cars[carID].location, newloc.client);

            // Ajouter une voiture à la liste des locations
            louée[*numloc] = newloc;
            (*numloc)++;

            printf("succés de l'operation.\n");
        } else {
            printf("la voiture est indisponible.\n");
        }
    } else {
        printf("Erreur.\n");
    }
}
//afficher la voiture ainsi que ses informations
void afficher(struct CAR cars[], int numCars) {
    printf("\nles voitures disponibles:\n");
    for (int i = 0; i < numCars; i++) {
        if (strcmp(cars[i].état, "disponible") == 0) {
            printf("ID: %d\n", i);
            printf("Immatriculation: %ld\n", cars[i].immatriculation);
            printf("Description: %s\n", cars[i].description);
            printf("prix: %.2f\n", cars[i].prix);
            printf("--------------\n");
        }
    }
}
//suppression d'une voiture de la liste 
void supprimer(struct CAR cars[], int *numCars) {
    printf("entrer l'ID de la voiture à supprimer: ");
    int carID;
    scanf("%d", &carID);
    if (carID >= 0 && carID < *numCars) {
        for (int i = carID; i < *numCars - 1; i++) {
            cars[i] = cars[i + 1];
        }

        (*numCars)--;
        printf("succés de l'operation.\n");
    } else {
        printf("ID invalide.\n");
    }
}
//mettre à jour les informations d'une voiture
void modifier(struct CAR cars[], int numCars) {
    printf("à quelle voiture vous voulez éffectuer la modification: ");
    int carID;
    scanf("%d", &carID);

    if (carID >= 0 && carID < numCars) {
        printf("la nouvelle description: ");
        scanf("%s", cars[carID].description);

        printf("le nouveau prix: ");
        scanf("%f", &cars[carID].prix);

        printf("succés de l'operation.\n");
    } else {
        printf("ID invalide.\n");
    }
}
//afficher l'historique de location 
void historique(struct LOCATION louée[], int numloc) {
    printf("\n historique de location:\n");
    for (int i = 0; i < numloc; i++) {
        printf("location ID: %d\n", louée[i].ID);
        printf("Immatriculation: %d\n", louée[i].immatriculation);
        printf("Client: %s\n", louée[i].client);
        printf("periode de location: %d days\n", louée[i].periode);
        printf("Total Tarif: %.2f\n", louée[i].tarif);
          printf("réclamer: %s\n", louée[i].réclamation);
        printf("--------------\n");
    }
}
//si vous avez une réclamation 
void reclamation(struct CAR cars[], int numCars, struct LOCATION louée[], int *numloc) {
    printf("Quelle voiture vous voulez réclamer : ");
    int locID;
    scanf("%d", &locID);

    if (locID >= 0 && locID < *numloc) {
        printf("Entrer votre réclamation: ");
         scanf("%s", louée[locID].réclamation);
        printf("votre réponse est enregistré.\n");
    } else {
        printf("ID invalide.\n");
    }
}
//ajouter une nouvelle voiture à la liste
void ajout(struct CAR cars[], int *numCars) {
    printf("Entrer la nouvelle immatriculation: ");
    scanf("%ld", &cars[*numCars].immatriculation);

    printf("Entrer la nouvelle description: ");
    scanf("%s", cars[*numCars].description);

    printf("Entrer le prix de la voiture: ");
    scanf("%f", &cars[*numCars].prix);
    strcpy(cars[*numCars].état, "disponible");
    (*numCars)++;
    printf("enregistrement effectué avec succés.\n");
}
