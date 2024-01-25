# sqlformatter

An opinionated SQL formatter. The primary goal of this formatter is producing
SQL code that is easier to visually parse by grouping semantic meaning on lines
and columns where possible. This is done by creating textual tables and 

## Examples

### CREATE TABLE

```sql
-- CREATE TABLE columns are separated into 3 sections:
-- 1. the column name
-- 2. the column type
-- 3. the column options
CREATE TABLE users (
  id         BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
  name       VARCHAR(255)    NOT NULL,
  created_at TIMESTAMP       DEFAULT CURRENT_TIMESTAMP,
  -- as needed, long lines can be broken up into multiple lines
  -- they are broken on semantic portions of the attributes
  updated_at TIMESTAMP       DEFAULT CURRENT_TIMESTAMP
                           ON UPDATE CURRENT_TIMESTAMP
);

CREATE TABLE pages (
  slug       VARCHAR(255) PRIMARY KEY,
  title      VARCHAR(255) NOT NULL,
  markdown   LONGTEXT     NOT NULL,
  created_at TIMESTAMP    DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP    DEFAULT CURRENT_TIMESTAMP
                        ON UPDATE CURRENT_TIMESTAMP
);

-- CONSTRAINTs are formatted similar to SELECT queries below, forming a river of
-- whitespace to increase legibility.
CREATE TABLE comments (
  id         BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
  user_id    BIGINT UNSIGNED NOT NULL,
  page_slug  VARCHAR(255)    NOT NULL,
  body       TEXT            NOT NULL,
  created_at TIMESTAMP       DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP       DEFAULT CURRENT_TIMESTAMP
                           ON UPDATE CURRENT_TIMESTAMP,

  CONSTRAINT comments_FK01
     FOREIGN KEY (user_id) REFERENCES users(id)
          ON UPDATE CASCADE
          ON DELETE CASCADE,
  CONSTRAINT comments_FK02
     FOREIGN KEY (page_slug) REFERENCES pages(slug)
          ON UPDATE CASCADE
          ON DELETE CASCADE
);
```

### INSERT

```sql
-- Queries form a river of whitespace after the first major keyword.
INSERT INTO users
     ( name)
VALUES
     ( 'John');

INSERT INTO pages
     -- Short lines can optionally be left on the same line.
     ( slug,         title
     , markdown)
VALUES
    -- An attempt is made to keep the corresponding values vertically aligned
    -- as well.
    ( 'hello-world', 'Hello World'
    , 'Hello world!');

INSERT INTO comments
     ( user_id, page_slug
     , body)
VALUES
     ( 1,       'hello-world'
     , 'Hello wrld'),
     ( 1,       'hello-world'
     , 'You say hello; while I say good world.');
```

### SELECT

```sql
-- SELECT queries form a river of whitespace after the first major keyword.
SELECT u.name AS user_name
     , c.body AS comment_body
     , c.created_at
     , c.updated_at
  FROM users AS u
  JOIN comments AS c
    ON c.user_id = u.id
  JOIN pages AS p
    ON c.page_slug = p.slug
 WHERE p.slug = 'hello-world'
```

### UPDATE

```sql
UPDATE users
   SET name = 'Johnny'
 WHERE id = 1;

UPDATE comments
   SET body = 'Hello world! (edit: spelling)'
 WHERE id = 1;

UPDATE pages
   SET markdown = '#Hello world!'
     , title = 'Helloooow World!'
 WHERE slug = 'hello-world';
```
