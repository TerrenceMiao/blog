---
title: SQL script generates random data and insert into MySQL database
date: 2017-03-03 23:02:17
tags:
---

``` SQL
DROP PROCEDURE InsertRandomRecords;
```

``` SQL
DELIMITER $$
CREATE PROCEDURE InsertRandomRecords(IN NumRows INT)
    BEGIN
        DECLARE i INT;
        SET i = 1;
        START TRANSACTION;
        WHILE i <= NumRows DO
            INSERT INTO draftorders.draft_order (c_number, source_time_in_ms, source_item_id, shipment, shipment_id, quantity, source_system, status)
            VALUES ('C01234567890', RAND()*1000000000, CONCAT('randomSourceRef-', UUID_SHORT()),
                    '{"to": {"name": "T T", "lines": ["Lvl 100", "123 smith st"], "phone": "0356567567", "state": "VIC", "suburb": "Greensborough", "postcode": "3088", "business_name": "In debt"}, "from": {"name": "Carl Block", "lines": ["1341 Dandenong Road"], "state": "VIC", "suburb": "Geelong", "postcode": "3220"}, "items": [{"width": "10", "height": "10", "length": "10", "weight": "10", "product_id": "3D85", "item_reference": "blocked", "authority_to_leave": true, "allow_partial_delivery": true, "contains_dangerous_goods": true}], "shipment_reference": "My second shipment ref", "customer_reference_1": "cr1234", "customer_reference_2": "cr5678"}',
                    UUID(), 1, 'EBAY', ELT(1 + FLOOR(RAND()*3), 'DRAFT', 'READY_TO_SHIP', 'SHIPPED'));
            SET i = i + 1;
        END WHILE;
        COMMIT;
    END$$
DELIMITER ;
```

To generate 1,000,000 draft orders:

``` SQL
CALL InsertRandomRecords(1000000);
```
