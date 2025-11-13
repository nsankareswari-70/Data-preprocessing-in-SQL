# Data-preprocessing-in-SQL
Project Objective:
To make the data accurate, consistent, and ready for modeling by handling issues like missing values, inconsistencies, and incorrect formats. 

Import data from Excel file.
```sql
select * from SqlDataExplore.dbo.housing;


-- Standardize date format

select SaleDate, convert(date,SaleDate) from SqlDataExplore.dbo.housing;

update housing set SaleDate=convert(date,SaleDate)

Alter table housing add saledateconverted date;

update housing set saledateconverted = convert(date,saledate)

```
```sql
select * from SqlDataExplore.dbo.housing;


-- Standardize date format

select SaleDate, convert(date,SaleDate) from SqlDataExplore.dbo.housing;

update housing set SaleDate=convert(date,SaleDate)

Alter table housing add saledateconverted date;

update housing set saledateconverted = convert(date,saledate)

select * from SqlDataExplore.dbo.housing;
```
<img src="https://github.com/nsankareswari-70/Data-preprocessing-in-SQL/blob/375b484cd7a1103077fc3f239700b8bbc1f36f11/hp1.png">
<img src="https://github.com/nsankareswari-70/Data-preprocessing-in-SQL/blob/375b484cd7a1103077fc3f239700b8bbc1f36f11/hp2.png">

```sql
-- Display Property Address data
select propertyaddress from SqlDataExplore.dbo.housing;
```

```sql
-- Check for missing address values
select * from SqlDataExplore.dbo.housing where propertyaddress is null order by ParcelID;

-- Self join tables
select a.parcelid,a.propertyaddress,b.parcelid,b.propertyaddress,isnull(a.propertyaddress,b.propertyaddress)
from SqlDataExplore.dbo.housing a join SqlDataExplore.dbo.housing b on
a.parcelid=b.parcelid and a.[UniqueID ]<>b.[UniqueID ] where a.PropertyAddress is null

-- Updating the null values
update a
set a.propertyaddress=isnull(a.propertyaddress,b.propertyaddress)
from SqlDataExplore.dbo.housing a
join SqlDataExplore.dbo.housing b
on a.parcelid=b.parcelid
and a.[UniqueID ]<>b.[UniqueID ]
where a.PropertyAddress is null

-- spliting the address into two columns and alter the table structure by adding two new columns and 
-- updating the address and city values.
select
substring(propertyaddress,1,charindex(',',propertyaddress)-1) as address,
substring(propertyaddress,charindex(',',propertyaddress)+1,len(propertyaddress))as address2
from SqlDataExplore.dbo.housing;

alter table SqlDataExplore.dbo.housing
add propertysplitaddress nvarchar(255);

update SqlDataExplore.dbo.housing
set propertysplitaddress=substring(propertyaddress,1,charindex(',',propertyaddress)-1)

alter table SqlDataExplore.dbo.housing
add propertysplitcity nvarchar(255)

update SqlDataExplore.dbo.housing
set propertysplitcity =
substring(propertyaddress,charindex(',',propertyaddress)+1,len(propertyaddress))


select * from SqlDataExplore.dbo.housing;

select PARSENAME(replace(Owneraddress,',','.'),3),
parsename(replace(owneraddress,',','.'),2),
parsename(replace(owneraddress,',','.'),1)
from SqlDataExplore.dbo.housing;

-- Adding 3 new columns to the table

alter table SqlDataExplore.dbo.housing
add ownersplitaddress nvarchar(255)

alter table SqlDataExplore.dbo.housing
add ownersplitcity nvarchar(255)

alter table SqlDataExplore.dbo.housing
add ownersplitstate nvarchar(255)

-- Inserting values to those columns
update SqlDataExplore.dbo.housing
set ownersplitaddress=PARSENAME(replace(Owneraddress,',','.'),3)

update SqlDataExplore.dbo.housing
set ownersplitcity = parsename(replace(owneraddress,',','.'),2)

update SqlDataExplore.dbo.housing
set ownersplitstate =parsename(replace(owneraddress,',','.'),1)

select * from SqlDataExplore.dbo.housing;
```
<img src="https://github.com/nsankareswari-70/Data-preprocessing-in-SQL/blob/375b484cd7a1103077fc3f239700b8bbc1f36f11/hp3.png">

```sql
-- viewing the distinct values of a column
select distinct(soldasvacant) from SqlDataExplore.dbo.housing;

-- viewing the distinct values and its count

select distinct(soldasvacant),count(*) as ct from SqlDataExplore.dbo.housing
group by SoldAsVacant order by ct ;
-- change Y to Yes and N to No in "Sold as vacant" field

```
<img src="https://github.com/nsankareswari-70/Data-preprocessing-in-SQL/blob/375b484cd7a1103077fc3f239700b8bbc1f36f11/hp4.png">

select soldasvacant,
case
    when soldasvacant ='y' then 'Yes'
	when soldasvacant = 'N' then 'No'
	else soldasvacant
	end
	from SqlDataExplore.dbo.housing;




update SqlDataExplore.dbo.housing
set soldasvacant =
case
    when soldasvacant ='y' then 'Yes'
	when soldasvacant = 'N' then 'No'
	else soldasvacant
	end

-- counting 'Yes' and 'No'
select soldasvacant,count(soldasvacant) from SqlDataExplore.dbo.housing
group by soldasvacant order by 2;

-- Finding Duplicates(104 duplicate values)
with rownumcte as(
select *,
row_number() over(
partition by parcelid,
propertyaddress,
saleprice,
saledate,
legalreference
order by
uniqueid
)row_num
from SqlDataExplore.dbo.housing
)
select * from rownumcte where row_num>1
order by PropertyAddress

-- Removing duplicates
with rownumcte as(
select *,
row_number() over(
partition by parcelid,
propertyaddress,
saleprice,
saledate,
legalreference
order by
uniqueid
)row_num
from SqlDataExplore.dbo.housing
)
delete from rownumcte where row_num>1
--order by PropertyAddress


select * from SqlDataExplore.dbo.housing;


--Delete Unused columns

select *
from SqlDataExplore.dbo.housing


alter table SqlDataExplore.dbo.housing
drop column propertyaddress,taxdistrict,owneraddress





