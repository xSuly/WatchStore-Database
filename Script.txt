-- etapa de DROP
alter table furnizeaza_ceas
drop constraint furnizeaza_ceas_ceasId_FK
drop constraint furnizeaza_ceas_comandaId_FK
drop constraint furnizeaza_ceas_pk;
drop table furnizeaza_ceas;

alter table detine_ceas
drop constraint detineceas_magazinId_FK
drop constraint detineceas_ceasId_FK
drop constraint detineceas_pk;
drop table detine_ceas;

alter table plasat
drop constraint plasat_ceasId_FK
drop constraint plasat_comandaId_FK
drop constraint plasat_pk;
drop table plasat;

alter table ceas
drop constraint ceas_ceasId_Pk;
drop table ceas;

alter table locatie
drop constraint locatie_locatieId_PK
drop constraint locatie_magazinId_fk;
drop table locatie;

alter table magazin
drop constraint magazin_magazinId_PK;
drop table magazin;

alter table brand_producator
drop constraint bp_brandId_PK;
drop table brand_producator;

alter table firma_de_livrat
drop constraint firmalivrat_firmaId_PK
drop constraint firmalivrat_comandaId_fk;
drop table firma_de_livrat;

alter table comanda
drop constraint comanda_comandaId_PK
drop constraint comanda_clientId_fk;
drop table comanda;

alter table client
drop constraint client_clientId_PK
drop constraint client_orasId_fk;
drop table client;

alter table oras
drop constraint oras_orasId_PK;
drop table oras;


--------------------------------
--- tabelul Magazin
create table magazin
(   magazin_id number(5) constraint magazin_magazinId_PK primary key,
    nume varchar2 (20) constraint magazin_nume_nn not null,
    program varchar2 (20) constraint magazin_program_nn not null,
    nr_telefon varchar2 (11) constraint magazin_nrTlf_nn not null constraint magazin_nrTlf_U unique constraint magazin_nrTlf_C check (length(nr_telefon)=10),
    email varchar2 (50) constraint magazin_email_nn not null constraint magazin_email_u unique,
    site_web varchar2 (50) constraint magazin_siteWeb_nn not null constraint magazin_siteWeb_U unique
);

--- tabelul Ceas
create table ceas
(   ceas_id number(6) constraint ceas_ceasId_Pk primary key,
    nume varchar2 (10) constraint ceas_nume_nn not null,
    model varchar2 (15) constraint ceas_model_nn not null,
    pret number (6,2) constraint ceas_pret_nn not null,
    tip_mecanism char(8) constraint ceas_tipMecanism_nn not null constraint ceas_tipMecanism_c check (tip_mecanism in ('Quartz','Mecanic')),
    tip_curea char (10) constraint ceas_tipCurea_nn not null constraint ceas_tipCurea_c check (tip_curea in ('Silicon','Material','Zale','Piele')),
    dimensiune_geam number (2) constraint ceas_dimensiuneGeam_nn not null,
    culoare varchar2 (20) constraint ceas_culoare_nn not null,
    afisaj char (10) constraint ceas_afisaj_nn not null constraint ceas_afisaj_c check (afisaj in ('Analogic','Digital')),
    an_fabricatie number (4) constraint ceas_anFabricatie_nn not null
);

--- tabelul Brand_producator
create table Brand_producator
(   brand_id number (6) constraint bp_brandId_PK primary key,
    nume varchar2 (15) constraint bp_nume_nn not null,
    tara_origine varchar2 (20) constraint bp_taraOrigine_nn not null
);

--- tabelul Oras
create table Oras
(   oras_id number (6) constraint oras_orasId_PK primary key,
    nume varchar2 (15) constraint oras_nume_nn not null,
    tara varchar2 (15) constraint oras_tara_nn not null,
    cod_postal number (6) constraint oras_codPostal_nn not null constraint oras_codPostal_u unique
);

--- tabelul Client
create table Client
(   client_id number(6) constraint client_clientId_PK primary key,
    nume varchar2 (20) constraint client_nume_nn not null,
    prenume varchar2 (20) constraint client_prenume_nn not null,
    nr_telefon number (11) constraint client_nrTlf_nn not null constraint client_nrTlf_c check (length(nr_telefon)=10) constraint client_nrTlf_u unique,
    email varchar2 (30) constraint client_email_nn not null constraint client_email_u unique,
    strada varchar2 (50) constraint client_strada_nn not null,
    oras_id number (6) constraint client_orasId_fk references Oras (oras_id) on delete set null
);

alter table Client
drop column nr_telefon;

alter table Client
add (nr_telefon varchar2(11) constraint client_nrTlf_nn not null constraint client_nrTlf_c check (length(nr_telefon)=10) constraint client_nrTlf_u unique); --am introdus nr_telefon drept number in loc de varchar2

--- tabelul Comanda
create table comanda
(   comanda_id number (6) constraint comanda_comandaId_PK primary key,
    data_amplasare date constraint comanda_dataAmplasare_nn not null,
    data_onorare_comanda date constraint comanda_dataOnorare_nn not null
);

alter table comanda
add (client_id number(6) constraint comanda_clientId_fk references Client (client_id) on delete cascade);-- am uitat sa introduc mai sus si am rulat


--- tabelul Firma_De_Livrat
create table firma_de_livrat
(   firma_id number (6) constraint firmalivrat_firmaId_PK primary key,
    nume varchar2 (15) constraint firmalivrat_nume_nn not null,
    comanda_id number (6) constraint firmalivrat_comandaId_fk references Comanda (comanda_id) on delete cascade
);

--- tabelul Plasat
create table plasat
(   ceas_id number (6) constraint plasat_ceasId_FK references ceas (ceas_id) on delete cascade,
    comanda_id number (6) constraint plasat_comandaId_FK references comanda (comanda_id) on delete cascade,
    constraint plasat_pk primary key (ceas_id, comanda_id)
);


--- tabelul Locatie
create table locatie
(   locatie_id number (6) constraint locatie_locatieId_PK primary key,
    oras varchar2 (15) constraint locatie_oras_nn not null,
    strada varchar2 (15) constraint locatie_strada_nn not null,
    magazin_id number(5) constraint locatie_magazinId_fk references Magazin (magazin_id) on delete set null
);

--- tabelul Furnizeaza_ceas
create table furnizeaza_ceas
(   ceas_id number (6) constraint furnizeaza_ceas_ceasId_FK references ceas (ceas_id) on delete cascade,
    brand_id number (6) constraint furnizeaza_ceas_comandaId_FK references brand_producator (brand_id) on delete cascade,
    constraint furnizeaza_ceas_pk primary key (ceas_id, brand_id)
);


--- tabelul Detine_ceas
create table detine_ceas
(   magazin_id number (5) constraint detineceas_magazinId_FK references magazin (magazin_id) on delete cascade,
    ceas_id number (6) constraint detineceas_ceasId_FK references ceas (ceas_id) on delete cascade,
    constraint detineceas_pk primary key (magazin_id, ceas_id)
);

-- inseram date/valori
insert into magazin
values(10000, 'Watchshop', '08-16', '0769123456', 'watchshop@gmail.com', 'watchshop.ro');
insert into magazin
values(10001, 'BBShop', '08-20', '0769456123', 'bbshop@gmail.com', 'bbshop.ro');
insert into magazin
values(10002, 'Victoria Watches', '10-18', '0769654321', 'victoriawatches@gmail.com', 'victoriawatches.ro');
insert into magazin
values(10003, 'TopWatch', '10-20', '0769654123', 'topwatch@gmail.com', 'topshop.ro');
insert into magazin
values(10004, 'AmazingWatches', '08-18', '0769000123', 'amazingwatches@gmail.com', 'amazingwatches.ro');

insert into locatie
values(250000, 'Ramnicu Sarat', 'B-dul 1 Dec1918', 10000);
insert into locatie
values(250001, 'Buzau', 'Principatelor', 10001);
insert into locatie
values(250002, 'Focsani', 'Str. Milcov', 10002);
insert into locatie
values(250003, 'Bucuresti', 'Iuliu Maniu', 10003);
insert into locatie
values(250004, 'Targoviste', 'B-dul Unirii', 10004);

commit;

insert into ceas
values(500000, 'Rolex', 'Mad Paris', 4350, 'Mecanic', 'Zale', 40, 'Gri', 'Analogic', 2020);

alter table ceas
modify pret number(10,2); --era prea putin 6,2

insert into ceas
values(500001, 'Fossil', 'Townsman', 827.99, 'Mecanic', 'Zale', 42, 'Gri', 'Analogic', 2018);
insert into ceas
values(500002, 'Atlantic', 'Superocean', 26900.00, 'Quartz', 'Piele', 40, 'Negru', 'Analogic', 2019);

alter table ceas
modify nume varchar2 (25); -- numele era prea scurt
alter table ceas
modify model varchar2 (25); -- la fel si modelul

insert into ceas
values(500003, 'ARMANI EXCHANGE', 'Armani Digital', 1049.95, 'Quartz', 'Zale', 41, 'Auriu', 'Digital', 2016);
insert into ceas
values(500004, 'Samsung', 'Galaxy Watch4 Classic', 1405.99, 'Mecanic', 'Silicon', 46, 'Alb', 'Digital', 2021);

commit;

alter table detine_ceas
add(cantitate_model number(2)); -- am uitat sa introduc cand am facut tabelul

insert into detine_ceas
values(10000, 500000, 2);
insert into detine_ceas
values(10001, 500001, 4);
insert into detine_ceas
values(10002, 500002, 3);
insert into detine_ceas
values(10003, 500003, 5);
insert into detine_ceas
values(10004, 500004, 15);

insert into brand_producator
values(800000, 'Rolex', 'Elvetia');
insert into brand_producator
values(800001, 'Fossil', 'Elvetia');
insert into brand_producator
values(800002, 'Atlantic', 'Elvetia');
insert into brand_producator
values(800003, 'ARMANI EXCHANGE', 'Italia');
insert into brand_producator
values(800004, 'Samsung', 'Coreea de Sud');

commit; --de siguranta

insert into furnizeaza_ceas
values(500000, 800000);
insert into furnizeaza_ceas
values(500001, 800001);
insert into furnizeaza_ceas
values(500002, 800002);
insert into furnizeaza_ceas
values(500003, 800003);
insert into furnizeaza_ceas
values(500004, 800004);

insert into oras
values (900000, 'Bucuresti', 'Romania', 400000);
insert into oras
values (900001, 'Cluj-Napoca', 'Romania', 400001);
insert into oras
values (900002, 'Varsovia', 'Polonia', 400002);
insert into oras
values (900003, 'Madrid', 'Spain', 400003);
insert into oras
values (900004, 'Amsterdam', 'Olanda', 400004);

insert into client
values(150000, 'Serban', 'Cosmin', 'jamboc92@gmail.com', 'Strada Scolii', 900000, '0727100200');
insert into client
values(150001, 'Sirbu', 'Ionut', 'alexs2001@gmail.com', 'B-dul Iuliu Maniu', 900001, '0727200300');
insert into client
values(150002, 'Robert', 'Lewandovski', 'rl9@gmail.com', 'Strada Prozna', 900002, '0727500500');
insert into client
values(150003, 'Sergio', 'Ramos', 'sr4@gmail.com', 'Strada Puerta del Sol', 900003, '0727100111');
insert into client
values(150004, 'Arjen', 'Robben', 'ar10@gmail.com', 'Strada Rockin', 900004, '0727502378');

insert into comanda
values(850000, '15-JAN-22', '17-JAN-22', 150000);
insert into comanda
values(850001, '10-JAN-22', '11-JAN-22', 150001);
insert into comanda
values(850002, '12-JAN-22', '25-JAN-22', 150002);
insert into comanda
values(850003, '14-JAN-22', '21-JAN-22', 150003);
insert into comanda
values(850004, '05-JAN-22', '12-JAN-22', 150004);

insert into firma_de_livrat
values (450000, 'FanCourier RO', 850000);
insert into firma_de_livrat
values (450001, 'NemoExpress', 850001);
insert into firma_de_livrat
values (450002, 'FanCourier INT', 850002);
insert into firma_de_livrat
values (450003, 'DHL', 850003);
insert into firma_de_livrat
values (450004, 'Pegasus', 850004);

insert into plasat
values (500000, 850000);
insert into plasat
values (500001, 850001);
insert into plasat
values (500002, 850002);
insert into plasat
values (500003, 850003);
insert into plasat
values (500004, 850004);
insert into plasat
values (500001, 850000);
insert into plasat
values (500002, 850000);

commit;

select client_id, c.nume, prenume, c.oras_id
from client c join oras o on c.oras_id=o.oras_id; --test

select ceas_id, model, c.nume
from ceas cs join plasat using(ceas_id)
join comanda using (comanda_id)
join client c using (client_id); --test

select magazin_id, m.nume, ceas_id, model, c.nume "Nume client"
from magazin m join detine_ceas using (magazin_id)
join ceas using (ceas_id)
join plasat using (ceas_id)
join comanda using (comanda_id)
join client c using (client_id); --test




