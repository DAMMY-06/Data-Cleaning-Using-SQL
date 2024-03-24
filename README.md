# Data-Cleaning-Using-SQL

Cleaning Data in SQL Queries

*/SELECT *
FROM PortfolioProject.dbo.[Nashville Housing Data for Data Cleaning]

 --Standardize Date Format
SELECT salesdateConverted,CONVERT(Date,SaleDate)
From [Nashville Housing Data for Data Cleaning]

ALTER TABLE [Nashville Housing Data for Data Cleaning]
Add SalesDateConverted Date;

Update [Nashville Housing Data for Data Cleaning]
SET SalesDateConverted = CONVERT(Date,SaleDate)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
-- Populate Property Address data
Select *
From [Nashville Housing Data for Data Cleaning]
--Where PropertyAddress is null
Order by ParcelID

SELECT a.ParcelID,a.PropertyAddress,b.ParcelID,b.PropertyAddress, ISNULL(a.PropertyAddress,b.PropertyAddress)
From [Nashville Housing Data for Data Cleaning] a
JOIN [Nashville Housing Data for Data Cleaning] b
 ON a.ParcelID = b.ParcelID
 AND a.UniqueID <> b.UniqueID
 Where a.PropertyAddress is null
 

 UPDATE a
 SET PropertyAddress = ISNULL(a.PropertyAddress,b.PropertyAddress)
 From [Nashville Housing Data for Data Cleaning] a
JOIN [Nashville Housing Data for Data Cleaning] b
ON a.ParcelID = b.ParcelID
 AND a.[UniqueID] <> b.[UniqueID]
Where a.PropertyAddress is null

--------------------------------------------------------------------------------------------------------------------------

-- Breaking out Address into Individual Columns (Address, City, State)

Select PropertyAddress
From [Nashville Housing Data for Data Cleaning]
--Where PropertyAddress is null
--Order by ParcelID

SELECT
SUBSTRING(PropertyAddress,1,CHARINDEX(',',PropertyAddress)-1) as Address
, SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress)+1 ,LEN(PropertyAddress)) as City
From [Nashville Housing Data for Data Cleaning]

ALTER TABLE [Nashville Housing Data for Data Cleaning]
Add PropertySplitAddress Nvarchar(200);

Update [Nashville Housing Data for Data Cleaning]
SET PropertySplitAddress = SUBSTRING(PropertyAddress,1,CHARINDEX(',',PropertyAddress)-1) 

ALTER TABLE [Nashville Housing Data for Data Cleaning]
Add PropertySplitCity Nvarchar(200)

UPDATE [Nashville Housing Data for Data Cleaning]
SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress)+1 ,LEN(PropertyAddress))

SELECT*
From [Nashville Housing Data for Data Cleaning]

Select OwnerAddress
From [Nashville Housing Data for Data Cleaning]

SELECT 
PARSENAME(REPLACE(OwnerAddress,',' , '.') ,3) 
,PARSENAME(REPLACE(OwnerAddress,',' , '.') ,2) 
,PARSENAME(REPLACE(OwnerAddress,',' , '.') ,1) 
From [Nashville Housing Data for Data Cleaning]

ALTER TABLE[Nashville Housing Data for Data Cleaning]
Add OwnerSplitAddress Nvarchar(255);

Update [Nashville Housing Data for Data Cleaning]
SET OwnerSplitAddress = PARSENAME(REPLACE(OwnerAddress, ',', '.') , 3)

ALTER TABLE [Nashville Housing Data for Data Cleaning]
Add OwnwerSplitCity Nvarchar (200);

UPDATE [Nashville Housing Data for Data Cleaning]
SET OwnwerSplitCity = PARSENAME(REPLACE(OwnerAddress,',' , '.') , 2) 

ALTER TABLE [Nashville Housing Data for Data Cleaning]
Add OwnwerSplitState Nvarchar (200);

 UPDATE [Nashville Housing Data for Data Cleaning]
SET OwnwerSplitState = PARSENAME(REPLACE(OwnerAddress,',' , '.') , 2) 

SELECT*
From [Nashville Housing Data for Data Cleaning]


--------------------------------------------------------------------------------------------------------------------------
-- Remove Duplicates

WITH RowNumCTE AS(
SELECT*,
  ROW_Number() OVER (
  PARTITION BY ParcelID,
				 PropertyAddress,
				 SalePrice,
				 SaleDate,
				 LegalReference 
				 ORDER BY
				  UniqueID
				 )Row_num
From [Nashville Housing Data for Data Cleaning]
--ORDER BY ParcelID
)
SELECT * 
From RowNumCTE
Where row_num > 1
Order by PropertyAddress

-----------------------------------------------------------------------------------------------------------------------------------------------------------

--Delete Unused Columns

Select*
From [Nashville Housing Data for Data Cleaning]

ALTER TABLE [Nashville Housing Data for Data Cleaning]
DROP COLUMN SalesDate


-------------------------------------------------------------------------------------------------------------------------------------------------
-- Round up Decimal Places


SELECT *
FROM [Nashville Housing Data for Data Cleaning]

Update [Nashville Housing Data for Data Cleaning]
SET Acreage = ROUND(Acreage,2)
