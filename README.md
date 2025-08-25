adbms ka code -- ============================================
-- Complete Transaction Demo: FeePayments Table (SQLite)
-- ============================================

-- Drop table if exists
DROP TABLE IF EXISTS FeePayments;

-- Create table
CREATE TABLE FeePayments (
    payment_id INTEGER PRIMARY KEY,
    student_name TEXT NOT NULL,
    amount REAL CHECK(amount > 0),
    payment_date TEXT NOT NULL
);

-- ============================================
-- Part A: Insert Multiple Fee Payments (COMMIT)
-- ============================================
BEGIN TRANSACTION;

INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES 
    (1, 'Ashish', 5000.00, '2024-06-01'),
    (2, 'Smaran', 4500.00, '2024-06-02'),
    (3, 'Vaibhav', 5500.00, '2024-06-03');

COMMIT;

SELECT 'Part A - After Commit' AS Stage;
SELECT * FROM FeePayments;

-- ============================================
-- Part B: Demonstrate ROLLBACK for Failed Insert
-- ============================================
BEGIN TRANSACTION;

-- Valid insert
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (4, 'Kiran', 6000.00, '2024-06-04');

-- Invalid insert (duplicate ID + negative amount)
-- This will fail and abort this transaction
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (1, 'Ashish', -500.00, '2024-06-05');

-- Rollback entire transaction
ROLLBACK;

SELECT 'Part B - After Rollback' AS Stage;
SELECT * FROM FeePayments;

-- ============================================
-- Part C: Partial Failure Demonstration
-- ============================================
BEGIN TRANSACTION;

-- Valid insert
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (5, 'Neha', 7000.00, '2024-06-06');

-- Invalid insert: NULL student_name
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (6, NULL, 6500.00, '2024-06-07');

-- Rollback entire transaction
ROLLBACK;

SELECT 'Part C - After Partial Failure Rollback' AS Stage;
SELECT * FROM FeePayments;

-- ============================================
-- Part D: Verify ACID Compliance
-- ============================================

-- First transaction with failure
BEGIN TRANSACTION;

-- Valid insert
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (7, 'Ritesh', 4800.00, '2024-06-08');

-- Invalid insert (duplicate ID)
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (3, 'Vaibhav', 5500.00, '2024-06-03');

-- Rollback due to failure
ROLLBACK;

-- Second transaction: successful commit
BEGIN TRANSACTION;
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (8, 'Anjali', 5200.00, '2024-06-09');
COMMIT;

SELECT 'Part D - Final Table State' AS Stage;
SELECT * FROM FeePayments;
