# sdalab11
 Ciurescu Radu Cosolan Sorina Balan Robert
#include <stdio.h>
#include <stdlib.h>
#include <string.h>


typedef struct TreeNode {  // structura pentru un nod in arborele binar de cautare
    char nume[50];
    int telefon;
    struct TreeNode* left;
    struct TreeNode* right;
} TreeNode;


TreeNode* createNode(char nume[], int telefon) {// functie pentru alocarea unui nou nod
    TreeNode* newNode = (TreeNode*)malloc(sizeof(TreeNode));
    strcpy(newNode->nume, nume);
    newNode->telefon = telefon;
    newNode->left = newNode->right = NULL;
    return newNode;
}


TreeNode* adaugaContact(TreeNode* root, char nume[], int telefon) {// functie pentru adaugarea unui nou contact in arbore
    if (root == NULL) {
        return createNode(nume, telefon);
    }

    int comparare = strcmp(nume, root->nume);

    if (comparare < 0) {
        root->left = adaugaContact(root->left, nume, telefon);
    }
    else if (comparare > 0) {
        root->right = adaugaContact(root->right, nume, telefon);
    }

    return root;
}


void cautaContact(TreeNode* root, char nume[]) {// functie pentru cautarea unui contact dupa nume si afisarea detaliilor acestuia
    if (root == NULL) {
        printf("Contactul \"%s\" nu a fost găsit.\n", nume);
        return;
    }

    int comparare = strcmp(nume, root->nume);

    if (comparare == 0) {
        printf("Detalii pentru contactul \"%s\":\n", nume);
        printf("Nume: %s\n", root->nume);
        printf("Telefon: %d\n", root->telefon);
    }
    else if (comparare < 0) {
        cautaContact(root->left, nume);
    }
    else {
        cautaContact(root->right, nume);
    }
}


TreeNode* minim(TreeNode* node) {// functie pentru gasirea nodului cu cea mai mica valoare din arbore
    TreeNode* current = node;
    while (current->left != NULL) {
        current = current->left;
    }
    return current;
}


TreeNode* stergeContact(TreeNode* root, char nume[]) {// functie pentru stergerea unui contact dupa nume
    if (root == NULL) {
        return root;
    }

    int comparare = strcmp(nume, root->nume);

    if (comparare < 0) {
        root->left = stergeContact(root->left, nume);
    }
    else if (comparare > 0) {
        root->right = stergeContact(root->right, nume);
    }
    else {
        
        if (root->left == NULL) {// nodul cu unul sau niciun copil
            TreeNode* temp = root->right;
            free(root);
            return temp;
        }
        else if (root->right == NULL) {
            TreeNode* temp = root->left;
            free(root);
            return temp;
        }

        
        TreeNode* temp = minim(root->right);// nodul cu doi copii: obtine succesorul în ordine si sterge succesorul
        strcpy(root->nume, temp->nume);
        root->telefon = temp->telefon;
        root->right = stergeContact(root->right, temp->nume);
    }

    return root;
}


void afiseazaInOrdine(TreeNode* root) {// functie pentru a afisa contactele in ordine lexicografica
    if (root != NULL) {
        afiseazaInOrdine(root->left);
        printf("Nume: %s, Telefon: %d\n", root->nume, root->telefon);
        afiseazaInOrdine(root->right);
    }
}

void elibereazaMemorie(TreeNode* root) {// functie pentru eliberarea memoriei alocate pentru arbore

    if (root != NULL) {
        elibereazaMemorie(root->left);
        elibereazaMemorie(root->right);
        free(root);
    }
}

int main() {
    TreeNode* radacina = NULL;

    radacina = adaugaContact(radacina, "John Doe", 123456789);
    radacina = adaugaContact(radacina, "Alice Smith", 987654321);
    radacina = adaugaContact(radacina, "Bob Johnson", 555555555);

    printf("Contacte in ordine lexicografica:\n");
    afiseazaInOrdine(radacina);

    cautaContact(radacina, "Alice Smith");

    radacina = stergeContact(radacina, "Alice Smith");

    printf("Contacte dupa stergere:\n");
    afiseazaInOrdine(radacina);

    elibereazaMemorie(radacina);

    return 0;
}
