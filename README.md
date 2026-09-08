# 🐾 Pet Salon Appointment Scheduling System

A client-server application developed for the **Software Design** (Projektovanje softvera) course at the Faculty of Organizational Sciences, University of Belgrade.

The system supports the operations of a pet grooming salon — managing groomers, pet owners, appointment scheduling, and service billing — through a Java desktop client that communicates with a server over TCP sockets, while all data is persisted in a MySQL database.

## 🧩 Architecture

The project is split into three NetBeans/Ant modules:

| Module | Description |
|---|---|
| **0ProjekatServer** | Server — accepts client connections, processes requests, and communicates with the database via JDBC |
| **0ProjekatKlijent** | Client — Swing desktop application with forms for login and data management |
| **0ProjekatZajednicki** | Shared library — domain classes and networking classes shared between client and server |

Communication runs over `java.net.Socket`/`ServerSocket` on port **9000**: the client sends a `Zahtev` (Request, containing an `Operacija` enum and payload data), the server processes it on a dedicated per-client thread, and returns an `Odgovor` (Response).

```
Client (Swing)  <──TCP socket, port 9000──>  Server  <──JDBC──>  MySQL database (seminarski)
```

### Server
- `Server` (extends `Thread`) listens for connections on `ServerSocket(9000)` and spawns a dedicated `ObradaKlijentskihZahteva` (client request handler) thread for each connected client
- Each request is processed following an **operation-per-class** pattern: the `operacija` package contains classes such as `LoginOperacija`, `DodajGrumeraSO`, `KreirajRacunSO`, `PretraziRacunSO`, etc., grouped by domain (groomers, owners, invoices, locations, shifts, services)
- Database access goes through the `repository/db` layer (`DbConnectionFactory`, `DbRepositoryGeneric`) using the MySQL JDBC driver
- Connection settings (host/port/database, credentials) are read from `config/config.properties`, and can also be edited through the server's GUI (`FormaKonfBaza`, `FormaKonfPort`)

### Client
- Swing desktop application; `Cordinator` (singleton) manages open forms and shared parameters
- `Komunikacija` (Communication, singleton) opens a socket to the server and sends/receives objects (`Posiljalac` / `Primalac` — sender/receiver)
- Forms: `LoginForma`, `GlavnaForma` (Main form), `PrikaziGrumeraForma`, `DodajGrumeraForma`, `PrikazVlasnikaPsaForma`, `DodajVlasnikaForma`, `PrikazRacunaForma`, `UbaciSmenaForma`

### Shared module
- Domain classes: `Grumer` (Groomer), `VlasnikPsa` (PetOwner), `Racun` (Invoice), `StavkaRacuna` (InvoiceItem), `VrstaUsluge` (ServiceType), `Mesto` (Location), `Smena` (Shift), `GrumerSmena` (GroomerShift)
- Communication classes: `Zahtev` (Request), `Odgovor` (Response), `Operacija` (enum of all supported operations), `Posiljalac` (Sender), `Primalac` (Receiver)

## 📱 Screens

| | | |
|:---:|:---:|:---:|
| **Server — before start**<br><img width="720" height="412" alt="image" src="https://github.com/user-attachments/assets/1819e9c6-986e-439d-ae27-efc0265f2801" />
E (server before start) | **Server — running**<br><img width="717" height="412" alt="image" src="https://github.com/user-attachments/assets/a62ae950-8702-406d-b195-0a9f39851b23" />
 (server running) | **Login**<br><img width="518" height="316" alt="image" src="https://github.com/user-attachments/assets/6c9a0fb5-e917-4e7b-8f2e-7f1192bf3127" />
 (login form) |
| **Main form — create invoice**<br><img width="801" height="770" alt="image" src="https://github.com/user-attachments/assets/54c2d80a-d366-4571-8514-03461330eee3" />
 (main form / invoice) | **Add pet owner**<br><img width="542" height="501" alt="image" src="https://github.com/user-attachments/assets/e31c08e1-daaf-40f5-a60b-1e5c3c0dc984" />
(add pet owner form) | **Add shift**<br><img width="453" height="372" alt="image" src="https://github.com/user-attachments/assets/c66ac5a2-e1d0-4608-84e0-61bed16ac765" />
E (add shift form) |

## 🗄️ Database

MySQL database `seminarski`, defined in [`baza.sql`](projekatPS/baza.sql), with tables:

`grumer`, `vlasnikpsa`, `racun`, `stavkaracuna`, `vrstausluge`, `mesto`, `smena`, `grumersmena`

## ⚙️ Tech Stack

- Java 17
- Swing (client and server GUI)
- Java Sockets (client-server networking)
- JDBC + MySQL Connector/J 8.0.21
- MySQL 8
- Ant / NetBeans project structure

## ▶️ Running the project

1. Import the schema from `projekatPS/baza.sql` into a local MySQL server
2. Configure the connection in `0ProjekatServer/config/config.properties` (database URL, USERNAME, PASSWORD, PORT)
3. Run `0ProjekatServer` (`main.Main`) — this opens the server GUI and starts listening on port 9000
4. Run `0ProjekatKlijent` (`main.Main`) — this opens the login form

> Note: `0ProjekatZajednicki` must be built first (or added as a dependency), since both the client and the server depend on it.

## 📎 Context

This project started as a coursework project for Software Design (mentor: prof. dr Siniša Vlajić), and was later extended and documented in detail as part of a bachelor's thesis.
