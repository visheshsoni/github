#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

// A program simulating a basic library management system

#define MAX_BOOKS 100
#define MAX_TITLE_LENGTH 100
#define MAX_AUTHOR_LENGTH 50
#define MAX_BORROWERS 50

typedef struct {
    int id;
    char title[MAX_TITLE_LENGTH];
    char author[MAX_AUTHOR_LENGTH];
    int is_borrowed;
} Book;

typedef struct {
    int id;
    char name[MAX_AUTHOR_LENGTH];
    int borrowed_book_id; // -1 if none
} Borrower;

Book library[MAX_BOOKS];
Borrower borrowers[MAX_BORROWERS];
int total_books = 0;
int total_borrowers = 0;

void add_book() {
    if (total_books >= MAX_BOOKS) {
        printf("Library is full. Cannot add more books.\n");
        return;
    }

    Book new_book;
    new_book.id = total_books + 1;
    printf("Enter book title: ");
    getchar();
    fgets(new_book.title, MAX_TITLE_LENGTH, stdin);
    new_book.title[strcspn(new_book.title, "\n")] = '\0';

    printf("Enter book author: ");
    fgets(new_book.author, MAX_AUTHOR_LENGTH, stdin);
    new_book.author[strcspn(new_book.author, "\n")] = '\0';

    new_book.is_borrowed = 0;

    library[total_books] = new_book;
    total_books++;
    printf("Book added successfully.\n");
}

void view_books() {
    if (total_books == 0) {
        printf("No books in the library.\n");
        return;
    }

    printf("\nBooks in Library:\n");
    printf("ID\tTitle\t\tAuthor\t\tStatus\n");
    for (int i = 0; i < total_books; i++) {
        printf("%d\t%s\t\t%s\t\t%s\n", library[i].id, library[i].title, library[i].author,
               library[i].is_borrowed ? "Borrowed" : "Available");
    }
}

void add_borrower() {
    if (total_borrowers >= MAX_BORROWERS) {
        printf("Borrower limit reached. Cannot add more borrowers.\n");
        return;
    }

    Borrower new_borrower;
    new_borrower.id = total_borrowers + 1;
    printf("Enter borrower name: ");
    getchar();
    fgets(new_borrower.name, MAX_AUTHOR_LENGTH, stdin);
    new_borrower.name[strcspn(new_borrower.name, "\n")] = '\0';

    new_borrower.borrowed_book_id = -1;

    borrowers[total_borrowers] = new_borrower;
    total_borrowers++;
    printf("Borrower added successfully.\n");
}

void view_borrowers() {
    if (total_borrowers == 0) {
        printf("No borrowers registered.\n");
        return;
    }

    printf("\nRegistered Borrowers:\n");
    printf("ID\tName\t\tBorrowed Book\n");
    for (int i = 0; i < total_borrowers; i++) {
        printf("%d\t%s\t\t%s\n", borrowers[i].id, borrowers[i].name,
               borrowers[i].borrowed_book_id == -1 ? "None" : library[borrowers[i].borrowed_book_id - 1].title);
    }
}

void borrow_book() {
    int borrower_id, book_id;

    printf("Enter borrower ID: ");
    scanf("%d", &borrower_id);
    if (borrower_id < 1 || borrower_id > total_borrowers) {
        printf("Invalid borrower ID.\n");
        return;
    }

    printf("Enter book ID: ");
    scanf("%d", &book_id);
    if (book_id < 1 || book_id > total_books) {
        printf("Invalid book ID.\n");
        return;
    }

    if (library[book_id - 1].is_borrowed) {
        printf("Book is already borrowed.\n");
        return;
    }

    if (borrowers[borrower_id - 1].borrowed_book_id != -1) {
        printf("Borrower already has a borrowed book.\n");
        return;
    }

    library[book_id - 1].is_borrowed = 1;
    borrowers[borrower_id - 1].borrowed_book_id = book_id;
    printf("Book borrowed successfully.\n");
}

void return_book() {
    int borrower_id;

    printf("Enter borrower ID: ");
    scanf("%d", &borrower_id);
    if (borrower_id < 1 || borrower_id > total_borrowers) {
        printf("Invalid borrower ID.\n");
        return;
    }

    if (borrowers[borrower_id - 1].borrowed_book_id == -1) {
        printf("This borrower has no borrowed books.\n");
        return;
    }

    int book_id = borrowers[borrower_id - 1].borrowed_book_id;
    library[book_id - 1].is_borrowed = 0;
    borrowers[borrower_id - 1].borrowed_book_id = -1;
    printf("Book returned successfully.\n");
}

void menu() {
    printf("\nLibrary Management System\n");
    printf("1. Add Book\n");
    printf("2. View Books\n");
    printf("3. Add Borrower\n");
    printf("4. View Borrowers\n");
    printf("5. Borrow Book\n");
    printf("6. Return Book\n");
    printf("7. Exit\n");
}

int main() {
    int choice;

    do {
        menu();
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                add_book();
                break;
            case 2:
                view_books();
                break;
            case 3:
                add_borrower();
                break;
            case 4:
                view_borrowers();
                break;
            case 5:
                borrow_book();
                break;
            case 6:
                return_book();
                break;
            case 7:
                printf("Exiting program. Goodbye!\n");
                break;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    } while (choice != 7);

    return 0;
}
