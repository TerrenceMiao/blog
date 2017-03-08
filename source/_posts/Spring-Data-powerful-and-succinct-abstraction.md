---
title: Spring Data - powerful and succinct abstraction
date: 2017-03-04 10:05:30
tags:
---

Database tier definition 
------------------------

Database tables, indexes and foreign keys defined in Liquibase configuration:

``` yaml
databaseChangeLog:
  - changeSet:
      id: 1
      author: Terrence Miao
      changes:
        - createTable:
            tableName: draft_order
            columns:
              - column:
                  name: id
                  type: int
                  autoIncrement: true
                  constraints:
                    primaryKey: true
                    nullable: false
              - column:
                  name: c_number
                  type: varchar(32)
                  constraints:
                    nullable: false
              - column:
                  name: source_time_in_ms
                  type: bigint
                  constraints:
                    nullable: false
              - column:
                  name: source_item_id
                  type: varchar(255)
                  constraints:
                    nullable: false
              - column:
                  name: shipment
                  type: json
                  constraints:
                    nullable: false
              - column:
                  name: shipment_id
                  type: varchar(255)
                  constraints:
                    nullable: true
              - column:
                  name: quantity
                  type: int
                  constraints:
                    nullable: false
              - column:
                  name: source_system
                  type: varchar(255)
                  constraints:
                    nullable: false
              - column:
                  name: status
                  type: varchar(32)
                  constraints:
                    nullable: false
        - createIndex:
            columns:
            - column:
                name: source_item_id
            indexName: idx_source_item_id
            tableName: draft_order
            unique: false
        - createIndex:
            columns:
            - column:
                name: c_number
            - column:
                name: source_item_id
            indexName: idx_c_number_source_item_id
            tableName: draft_order
            unique: true
        - createTable:
            tableName: draft_order_combined
            columns:
              - column:
                  name: id
                  type: int
                  autoIncrement: true
                  constraints:
                    primaryKey: true
                    nullable: false
              - column:
                  name: combined_id
                  type: varchar(64)
                  constraints:
                    nullable: false
              - column:
                  name: draft_order_id
                  type: int
                  constraints:
                    nullable: false
        - addForeignKeyConstraint:
            baseColumnNames: draft_order_id
            baseTableName: draft_order_combined
            constraintName: fk_draft_order_combined_draft_order
            onDelete: CASCADE
            onUpdate: RESTRICT
            referencedColumnNames: id
            referencedTableName: draft_order
  - changeSet:
      id: 2
      author: Terrence Miao
      changes:
        - addColumn:
            columns:
              - column:
                  # For MySQL 5.7.x above, the first TIMESTAMP column in the table gets current timestamp as the default value, likely. So 
                  # if an INSERT or UPDATE without supplying a value, the column will get the current timestamp.  Any subsequent TIMESTAMP 
                  # columns should have a default value explicitly defined. If you have two TIMESTAMP columns and if you don't specify a 
                  # default value for the second column, you will get this error while trying to create the table: 
                  #   ERROR 1067 (42000): Invalid default value for 'COLUMN_NAME'
                  name: date_created
                  type: timestamp(3)
                  constraints:
                    nullable: false
              - column:
                  name: date_updated
                  type: timestamp(3)
		  defaultValueComputed: LOCALTIMESTAMP(3)
                  constraints:
                    nullable: false
            tableName: draft_order
```

DAO definition
--------------

- Draft Order

``` Java
@Entity
@Table(name = "draft_order")
public class DraftOrder implements Serializable {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Integer id;

    @Column(name = "c_number")
    private String cNumber;

    @Column(name = "source_time_in_ms")
    private Long sourceTimeInMs;

    @Column(name = "source_item_id")
    private String sourceItemId;

    @Column(name = "shipment", columnDefinition = "json")
    private String shipment;

    @Column(name = "shipment_id")
    private String shipmentId;

    @Column(name = "quantity")
    private Integer quantity;

    @Column(name = "source_system")
    private String sourceSystem;

    @Column(name = "status")
    private String status;
}
```

- Draft Order Combined 

``` Java
@Entity
@Table(name = "draft_order_combined")
public class DraftOrderCombined implements Serializable {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Integer id;

    @Column(name = "combined_id")
    private String combinedId;

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "draft_order_id")
    private DraftOrder draftOrder;
}
```

- An middle Aggregation Object

``` Java
public class CombinedIdSourceTimeInMs {

    private Long counter;
    private String combinedId;
    private Long sourceTimeInMs;

    public CombinedIdSourceTimeInMs(Long counter, String combinedId, Long sourceTimeInMs) {
        this.counter = counter;
        this.combinedId = combinedId;
        this.sourceTimeInMs = sourceTimeInMs;
    }
}
```

CRUD Repository definition
--------------------------

- DraftOrderRepository

``` Java
public interface DraftOrderRepository extends CrudRepository<DraftOrder, Integer> {

    List<DraftOrder> findByCNumberAndStatusOrderBySourceTimeInMsDesc(String cNumber, String status, Pageable pageable);

    List<DraftOrder> findByCNumberAndSourceItemIdIn(String cNumber, List<String> sourceItemIds);

    DraftOrder findByCNumberAndSourceItemId(String cNumber, String sourceItemId);
    
    List<DraftOrder> findByShipmentIdInAndStatusAndSourceSystem(List<String> shipmentIds, String status, String sourceSystem);

    List<DraftOrder> findByCNumberAndId(String cNumber, Integer id);

    Long countByCNumberAndStatus(String cNumber, String status);
}
```

- DraftOrderCombinedRepository

``` Java
public interface DraftOrderCombinedRepository extends CrudRepository<DraftOrderCombined, Integer> {

    String FIND_QUERY =
            "SELECT new org.paradise.data.dao.CombinedIdSourceTimeInMs"
                    + "(count(doc) as counter, doc.combinedId as combinedId, min(doc.draftOrder.sourceTimeInMs) as sourceTimeInMs) "
                    + " FROM DraftOrderCombined doc WHERE doc.draftOrder.cNumber = :cNumber AND doc.draftOrder.status = :status "
                    + " GROUP BY combinedId "
                    + " ORDER BY sourceTimeInMs DESC";

    String COUNT_QUERY = "SELECT count(1) FROM "
            + "(SELECT count(1) FROM DraftOrderCombined doc WHERE doc.draftOrder.cNumber = :cNumber AND doc.draftOrder.status = :status"
            + "  GROUP BY doc.combinedId)";

    @Query(value = FIND_QUERY, countQuery = COUNT_QUERY)
    List<CombinedIdSourceTimeInMs> countPerCombinedIdAndSourceTimeInMs(@Param("cNumber") String cNumber,
                                                                       @Param("status") String status, Pageable pageable);

    List<DraftOrderCombined> findByCombinedIdOrderByDraftOrderDaoSourceTimeInMsDesc(String combinedId);
}
```

References
==========

- Spring Data JPA Reference Documentation, https://docs.spring.io/spring-data/jpa/docs/current/reference/html/
