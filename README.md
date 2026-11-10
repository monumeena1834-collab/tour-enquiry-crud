# tour-enquiry-crud
Tour Enquiry Management System using C programming with CRUD operations, structures, pointers and dynamic memory allocation.

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Enquiry {
    int id;
    char name[50];
    char phone[15];
    char destination[50];
    char travelDate[20];
    int persons;
};

void addEnquiry() {
    FILE *fp;
    struct Enquiry e;

    fp = fopen("tour.dat", "ab");

    if (fp == NULL) {
        printf("File opening error!\n");
        return;
    }

    printf("\nEnter Enquiry ID: ");
    scanf("%d", &e.id);

    printf("Enter Customer Name: ");
    scanf(" %49[^\n]", e.name);

    printf("Enter Phone Number: ");
    scanf("%14s", e.phone);

    printf("Enter Destination: ");
    scanf(" %49[^\n]", e.destination);

    printf("Enter Travel Date (DD/MM/YYYY): ");
    scanf("%19s", e.travelDate);

    printf("Enter Number of Persons: ");
    scanf("%d", &e.persons);

    fwrite(&e, sizeof(e), 1, fp);

    fclose(fp);

    printf("\nEnquiry Added Successfully!\n");
}

void viewEnquiries() {
    FILE *fp;
    struct Enquiry e;

    fp = fopen("tour.dat", "rb");

    if (fp == NULL) {
        printf("\nNo records found!\n");
        return;
    }

    printf("\n----- TOUR ENQUIRIES -----\n");

    while (fread(&e, sizeof(e), 1, fp) == 1) {
        printf("\nID: %d", e.id);
        printf("\nName: %s", e.name);
        printf("\nPhone: %s", e.phone);
        printf("\nDestination: %s", e.destination);
        printf("\nTravel Date: %s", e.travelDate);
        printf("\nPersons: %d\n", e.persons);
    }

    fclose(fp);
}

void updateEnquiry() {
    FILE *fp;
    struct Enquiry e;
    int id, found = 0;

    fp = fopen("tour.dat", "rb+");

    if (fp == NULL) {
        printf("\nNo records found!\n");
        return;
    }

    printf("\nEnter Enquiry ID to update: ");
    scanf("%d", &id);

    while (fread(&e, sizeof(e), 1, fp) == 1) {
        if (e.id == id) {
            printf("Enter New Customer Name: ");
            scanf(" %49[^\n]", e.name);

            printf("Enter New Phone Number: ");
            scanf("%14s", e.phone);

            printf("Enter New Destination: ");
            scanf(" %49[^\n]", e.destination);

            printf("Enter New Travel Date: ");
            scanf("%19s", e.travelDate);

            printf("Enter New Number of Persons: ");
            scanf("%d", &e.persons);

            fseek(fp, -(long)sizeof(e), SEEK_CUR);
            fwrite(&e, sizeof(e), 1, fp);

            found = 1;
            break;
        }
    }

    fclose(fp);

    if (found)
        printf("\nEnquiry Updated Successfully!\n");
    else
        printf("\nEnquiry ID Not Found!\n");
}

void deleteEnquiry() {
    FILE *fp, *temp;
    struct Enquiry e;
    int id, found = 0;

    fp = fopen("tour.dat", "rb");

    if (fp == NULL) {
        printf("\nNo records found!\n");
        return;
    }

    temp = fopen("temp.dat", "wb");

    if (temp == NULL) {
        fclose(fp);
        printf("Temporary file error!\n");
        return;
    }

    printf("\nEnter Enquiry ID to delete: ");
    scanf("%d", &id);

    while (fread(&e, sizeof(e), 1, fp) == 1) {
        if (e.id == id) {
            found = 1;
        } else {
            fwrite(&e, sizeof(e), 1, temp);
        }
    }

    fclose(fp);
    fclose(temp);

    if (found) {
        if (remove("tour.dat") != 0 ||
            rename("temp.dat", "tour.dat") != 0) {
            printf("Error replacing data file!\n");
            return;
        }
        printf("\nEnquiry Deleted Successfully!\n");
    } else {
        remove("temp.dat");
        printf("\nEnquiry ID Not Found!\n");
    }
}

int main() {
    int choice;

    while (1) {
        printf("\n================================");
        printf("\n TOUR ENQUIRY MANAGEMENT SYSTEM");
        printf("\n================================");
        printf("\n1. Add Enquiry");
        printf("\n2. View Enquiries");
        printf("\n3. Update Enquiry");
        printf("\n4. Delete Enquiry");
        printf("\n5. Exit");
        printf("\nEnter Your Choice: ");

        if (scanf("%d", &choice) != 1)
            break;

        switch (choice) {
            case 1:
                addEnquiry();
                break;

            case 2:
                viewEnquiries();
                break;

            case 3:
                updateEnquiry();
                break;

            case 4:
                deleteEnquiry();
                break;

            case 5:
                printf("\nThank You!\n");
                exit(0);

            default:
                printf("\nInvalid Choice! Try Again.\n");
        }
    }

    return 0;
}
