--
-- table structure for table publishers
--

create table publishers(publisherid int primary key auto_increment, name varchar(50) unique not null, address varchar(100)); 

--
-- table structure for table books
--

create table books(accno int primary key auto_increment, title varchar(100) unique not null, author varchar(50) not null, price float(6,2), type enum('lending','reference'), availability enum('yes','no'), publisherid int); 
alter table books add constraint books_fkey_publisherid foreign key(publisherid) references publishers(publisherid);

--
-- table structure for table members
--

create table members(memberid int primary key auto_increment, name varchar(50) unique not null, address varchar(100), type enum('staff','student')); 

--
-- table structure for table issues
--

create table issues(issueid int primary key auto_increment, memberid int, accno int, issuedate date, duedate date); 
alter table issues add constraint issues_fkey_memberid foreign key(memberid) references members(memberid); 
alter table issues add constraint issues_fkey_accno foreign key(accno) references books(accno);

--
-- table structure for table returns
--

create table returns(returnid int primary key auto_increment, memberid int, accno int, returndate date, fine int default 0 check(fine>=0)); 
alter table returns add constraint returns_fkey_memberid foreign key(memberid) references members(memberid); 
alter table returns add constraint returns_fkey_accno foreign key(accno) references books(accno);

--
-- dumping data for table books
--

insert into books(title, author, price, type, availability, publisherid) values 
("dracula", "kottayam pushpanath", 600.00, 'lending', 'yes', 2), 
("john sick", "fazal", 50.00, 'reference', 'no', 1), 
("kayar", "thakazhi", 700.00, 'lending', 'yes', 3), 
("im leend", "sura", 1.00, 'reference', 'yes', 1), 
("doomsday clock", "john", 100.00, 'lending', 'yes', 5); 

--
-- dumping data for table publishers
--

insert into publishers(name, address) values 
("suku publications", "paris"), 
("ramesh books", "kunnamkulam"), 
("dc books", "kuttippuram"), 
("marvel comics", "new york"), 
("dc comics", "washington dc"); 

--
-- dumping data for table members
--

insert into members(name, address, type) values 
('appu', 'tavanur', 'student'), 
('saran', 'pattambi', 'student'), 
('bob', 'valayamkulam', 'staff'), 
('kevin', 'munnar', 'staff'), 
('lucy', 'edappal', 'staff'); 

--
-- dumping data for table issues
--

insert into issues(memberid, accno, issuedate, duedate) values 
(1, 2, '2024-01-12', '2024-01-14'), 
(2, 3, '2025-02-02', '2025-02-02'), 
(3, 3, '2024-12-15', '2025-01-03'), 
(1, 4, '2025-01-01', '2025-01-05'), 
(4, 5, '2025-03-15', '2025-03-25'); 

--
-- dumping data for table returns
--

insert into returns(memberid, accno, returndate, fine) values 
(1, 2, '2024-12-12', 0), 
(2, 3, '2025-02-02', 100), 
(4, 3, '2025-03-02', 500), 
(2, 5, '2023-12-02', 100), 
(5, 1, '2024-04-15', 0);

--
-- SQl Query Statements
--

--Retrieve all members with their name and address.
select name, address from members;

--Retrieve all books (include only accno and title) containing a letter ‘o’ anywhere in the title.
select accno, title from books where title like '%o%';

--Retrieve books (include all attributes) that are currently available for lending. 
select * from books where type = 'lending' and availability = 'yes';

--Retrieve all books with their title, author, and corresponding publisher name. 
select b.title, b.author, p.name from books b join publishers p on b.publisherid = p.publisherid;

--Retrieve members who issued books with their name, address, and corresponding book titles in ascending order of title.

select m.name, m.address, b.title from members m join issues i on m.memberid = i.memberid join books b on i.accno = b.accno order by b.title asc;

--Retrieve the name, total number of books (rename it as TotalBooksIssued) issued by each member.
select m.name, count(i.accno) as totalbooksissued from members m left join issues i on m.memberid = i.memberid group by m.name;

--Retrieve members (include name, address) who have not issued any books.
select m.name, m.address from members m left join issues i on m.memberid = i.memberid where i.issueid is null;

--Retrieve all books with their title, author, and the total number of time (rename it as NumberofIssue) each book has been issued.
select b.title, b.author, count(i.accno) as numberofissue from books b left join issues i on b.accno = i.accno group by b.title, b.author;

--Retrieve members (include name, TotalBooksIssued) who have issued more than 2 books.
select m.name, count(i.accno) as totalbooksissued from members m join issues i on m.memberid = i.memberid group by m.name having count(i.accno) > 2;

--Retrieve books (include title, author) that have not been issued yet.
select b.title, b.author from books b left join issues i on b.accno = i.accno where i.issueid is null;

--Retrieve the top 5 most issued books (include title, author, TotalIssued).
select b.title, b.author, count(i.accno) as totalissued from books b join issues i on b.accno = i.accno group by b.title, b.author order by totalissued desc limit 5;

--Retrieve the most issued books (include title, author, TotalIssued).
select b.title, b.author, count(i.accno) as totalissued from books b join issues i on b.accno = i.accno group by b.title, b.author order by totalissued desc limit 1;

--Retrieve members (include name, issuedate) who issued books before 2023-06-01 and have not returned them yet.
select m.name, i.issuedate from members m join issues i on m.memberid = i.memberid left join returns r on i.accno = r.accno and i.memberid = r.memberid where i.issuedate < '2023-06-01' and r.returnid is null;


--Create a MySQL view named "IssuedBooksDetails" that combines information about issued books, including the book title, member name, and issue date. Include columns for book title, member name, and issue date.

create view IssuedBooksDetails as select b.title,m.name,i.issuedate from members m join issues i on m.memberid = i.memberid join books b on i.accno=b.accno;

--Create a stored function in MySQL that calculates and returns the total fine amount for a given member based on their memberid.

delimiter //
create function TotalFine(memid int) returns int reads sql data begin declare finee int; select sum(fine) into finee from returns where memberid=memid; return
finee; end//
delimiter ;

--Create a MySQL stored procedure named "AddIssue" to facilitate issuing a book to a member. The procedure should take input parameters for the accession number of the book and the member ID of the member. It should insert to the Issues table with the new issue record, including the current date as the issue date. Use the built-in function CURDATE() to get the current date.

delimiter //
create procedure Addissue(IN accno int,IN memberid int) begin insert into issues(memberid,accno,issuedate,duedate) values (memberid,accno,curdate(),curdate()+interval 30 day); end//
delimiter ;

--Create a stored procedure 'IsAvailable' which takes an accession number of a book as input and outputs a boolean value (TRUE/FALSE) on the availability of that book.

delimiter //
create procedure IsAvailable(IN acc int,OUT isTaken boolean) begin declare available varchar(3); select availability into available from books where accno=acc; set isTaken=IF(available='yes',TRUE,FALSE); end//
delimiter ;


--Create a MySQL trigger named 'UpdateAvailabilityToNo' to update the availability of a book to "no" once that books is issued to some member.

delimiter //
create trigger UpdateAvailabilityToNo
after insert on issues
for each row
begin
update books set availability='no' where accno=new.accno;
end//
delimiter ;

--Create a MySQL trigger named 'UpdateAvailabilityToYes' to update the availability of a book to "yes" once that books is returned to library.

delimiter //
create trigger UpdateAvailabilityToYes
after insert on returns
for each row
begin
update books set availability='yes' where accno=new.accno;
end//
delimiter ;
