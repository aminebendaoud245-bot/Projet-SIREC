# Projet-SIREC
DROP DATABASE IF EXISTS SIREC;
CREATE DATABASE SIREC;
USE SIREC;
-- Table: Profil_Systeme
CREATE TABLE Profil_Systeme (
    id_profil INT PRIMARY KEY AUTO_INCREMENT,
    code_profil VARCHAR(30) NOT NULL,
    libelle_profil VARCHAR(100) NOT NULL
);
-- Table: Mode_Paiement (NOUVELLE TABLE)
CREATE TABLE Mode_Paiement (
    id_mode INT PRIMARY KEY AUTO_INCREMENT,
    code_mode VARCHAR(20) NOT NULL,
    libelle_mode VARCHAR(50) NOT NULL
);
-- 3. TABLES PRINCIPALES
-- Table: Post_Comptable
CREATE TABLE Post_Comptable (
    id_poste INT PRIMARY KEY AUTO_INCREMENT,
    intitule_poste VARCHAR(100) NOT NULL,
    ville_post VARCHAR(50),
    province_poste VARCHAR(50),
    date_mise_service DATE,
    date_fin_service DATE
);
-- Table: Fonctionnaire
CREATE TABLE Fonctionnaire (
    id_fonctionnaire INT PRIMARY KEY AUTO_INCREMENT,
    nom VARCHAR(50) NOT NULL,
    prenom VARCHAR(50) NOT NULL,
    fonction VARCHAR(50),
    date_debut_service DATE NOT NULL,
    date_fin_service DATE,
    id_poste INT NOT NULL,
    FOREIGN KEY (id_poste) REFERENCES Post_Comptable(id_poste)
);
-- Table: Utilisateur
CREATE TABLE Utilisateur (
    id_user INT PRIMARY KEY AUTO_INCREMENT,
    login_user VARCHAR(50) NOT NULL UNIQUE,
    password_user VARCHAR(255) NOT NULL,
    email VARCHAR(100),
    id_fonctionnaire INT NOT NULL,
    id_poste INT NOT NULL,
    id_profil INT NOT NULL,
    FOREIGN KEY (id_fonctionnaire) REFERENCES Fonctionnaire(id_fonctionnaire),
    FOREIGN KEY (id_poste) REFERENCES Post_Comptable(id_poste),
    FOREIGN KEY (id_profil) REFERENCES Profil_Systeme(id_profil)
);
-- Table: Ordonnateur
CREATE TABLE Ordonnateur (
    id_ordonnateur INT PRIMARY KEY AUTO_INCREMENT,
    nom_ordonnateur VARCHAR(100) NOT NULL
);
-- Table: Contribuable
CREATE TABLE Contribuable (
    id_contribuable INT PRIMARY KEY AUTO_INCREMENT,
    adresse VARCHAR(255),  
    ville VARCHAR(50),
    code_postale VARCHAR(20),
    email VARCHAR(50),
    situation_rec VARCHAR(50)
);
-- Table: Nature_Impot 
CREATE TABLE Nature_Impot (
    code_impot INT PRIMARY KEY AUTO_INCREMENT,
    intitule_impot VARCHAR(100) NOT NULL,
    taux DECIMAL(5,2),
    abreviation_impot VARCHAR(50)
);
-- Table: Role_Imposition 
CREATE TABLE Role_Imposition (
    num_pec_role INT PRIMARY KEY AUTO_INCREMENT,  
    code_impot INT,
    num_secteur INT,
    code_impression INT,
    annee_imposition INT,
    date_mise_recouvrement DATE,
    FOREIGN KEY (code_impot) REFERENCES Nature_Impot(code_impot)
);
-- Table: Rubrique_Budgetaire
CREATE TABLE Rubrique_Budgetaire (
    id_rubrique INT PRIMARY KEY AUTO_INCREMENT,  
    num_chapitre VARCHAR(50),
    num_article VARCHAR(50),
    num_paragraphe VARCHAR(50),
    ligne_rubrique VARCHAR(50),
    libelle_rubrique VARCHAR(100)
);
-- Table: Bordereau
CREATE TABLE Bordereau (
    id_bordereau VARCHAR(50) PRIMARY KEY,
    date_emission DATE,
    date_prise_charge DATE,
    montant_global DECIMAL(15,2), 
    id_ordonnateur INT,
    FOREIGN KEY (id_ordonnateur) REFERENCES Ordonnateur(id_ordonnateur)
);
-- Table: Article_Recette
CREATE TABLE Article_Recette (
    id_article VARCHAR(50) PRIMARY KEY,
    montant DECIMAL(15,2),  
    date_emission DATE,
    date_exigibilite DATE,
    etat_article VARCHAR(50),  
    id_bordereau VARCHAR(50),
    id_contribuable INT,
    code_impot INT,   
    id_rubrique INT,  
    FOREIGN KEY (id_bordereau) REFERENCES Bordereau(id_bordereau),
    FOREIGN KEY (id_contribuable) REFERENCES Contribuable(id_contribuable),
    FOREIGN KEY (code_impot) REFERENCES Nature_Impot(code_impot),
    FOREIGN KEY (id_rubrique) REFERENCES Rubrique_Budgetaire(id_rubrique)
);
-- Table: Operation_Recette
CREATE TABLE Operation_Recette (
    id_operation INT PRIMARY KEY AUTO_INCREMENT,
    date_operation DATE,
    montant_operation DECIMAL(15,2),
    mode_paiement INT,  
    id_article VARCHAR(50),
    id_fonctionnaire INT,
    FOREIGN KEY (id_article) REFERENCES Article_Recette(id_article),
    FOREIGN KEY (id_fonctionnaire) REFERENCES Fonctionnaire(id_fonctionnaire),
    FOREIGN KEY (mode_paiement) REFERENCES Mode_Paiement(id_mode)  
);
-- Table: Detail_Recette
CREATE TABLE Detail_Recette (
    id_detail INT PRIMARY KEY AUTO_INCREMENT,
    montant_detail DECIMAL(15,2),
    id_operation INT,
    FOREIGN KEY (id_operation) REFERENCES Operation_Recette(id_operation)
);
-- Table: Imputer
CREATE TABLE Imputer (
    id_rubrique INT,
    id_operation INT,
    PRIMARY KEY (id_operation, id_rubrique),
    FOREIGN KEY (id_operation) REFERENCES Operation_Recette(id_operation),
    FOREIGN KEY (id_rubrique) REFERENCES Rubrique_Budgetaire(id_rubrique)
);
-- Table: Proposition_Non_Valeur
CREATE TABLE Proposition_Non_Valeur (
    id_pnv INT PRIMARY KEY AUTO_INCREMENT,
    date_pnv DATE,
    statut_pnv VARCHAR(50),
    id_article VARCHAR(50),
    FOREIGN KEY (id_article) REFERENCES Article_Recette(id_article)
);
-- Table: Admission_Non_Valeur
CREATE TABLE Admission_Non_Valeur (
    id_anv INT PRIMARY KEY AUTO_INCREMENT,
    date_anv DATE,
    statut_anv VARCHAR(50),
    id_pnv INT,
    FOREIGN KEY (id_pnv) REFERENCES Proposition_Non_Valeur(id_pnv)
);
-- 4. INDEX POUR OPTIMISER LES PERFORMANCES
CREATE INDEX idx_article_bordereau ON Article_Recette(id_bordereau);
CREATE INDEX idx_article_contribuable ON Article_Recette(id_contribuable);
CREATE INDEX idx_operation_article ON Operation_Recette(id_article);
CREATE INDEX idx_operation_fonctionnaire ON Operation_Recette(id_fonctionnaire);
CREATE INDEX idx_utilisateur_login ON Utilisateur(login_user);
