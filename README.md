# Building-area-per-parcel
calculate area of all buildings within each parcel. Based off of Mike's 'Calculate percentage of each borough that is occupied by buildings' script


-- GITHUB script: Mike

--SQL 

-- Calculate percentage of each borough that is occupied by buildings

SELECT boroname, boro_area, sum(ST_Area(geom_2263)/43560) AS bldgarea, sum(ST_Area(geom_2263)/43560)/boro_area as propbldg -- converting to acre

FROM (
  SELECT nycbldgs.bin, nycbldgs.geom_2263, boroughs_nowater.boroname, st_area(boroughs_nowater.geom_2263)/43560 as boro_area -- joining buildings on boroughs
  
  FROM staging.boroughs_nowater JOIN staging.nycbldgs ON ST_Intersects(nycbldgs.geom_2263, boroughs_nowater.geom_2263)
) sub
GROUP BY boroname, boro_area


-- join of parcel geometry, total building area (all parcels) and bbl (unique identifier)

select mappluto_citywide17v1x1.geom_2263, sum(bldgs_20180611.shape_area) as build_area,
mappluto_citywide17v1x1.bbl
from infrastructure.bldgs_20180611 left join admin.mappluto_citywide17v1x1 on bbl=bbl where ownertype='P' and block = 4539
group by mappluto_citywide17v1x1.geom_2263, mappluto_citywide17v1x1.bbl;


-- combine both chunks
