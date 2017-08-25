---
layout: post
title:  "Automatically truncate a string before inserting to PostgreSQL"
date:   2017-08-25 00:00:00
---

This trick doesn't work with a row of type `varchar(3)`, I don't know why.

{% highlight sql %}
BEGIN;
CREATE TABLE t (
  z text,
  CHECK (length(z) <= 3)
);
CREATE FUNCTION
  f()
  RETURNS trigger
  LANGUAGE plpgsql
  AS '
  BEGIN
    NEW.z := substr(NEW.z,1,3);
    RETURN NEW;
  END';
CREATE TRIGGER tr BEFORE INSERT OR UPDATE
  ON t
  FOR EACH ROW
  EXECUTE PROCEDURE f();
INSERT INTO t(z)
  VALUES ('qwerty')
  RETURNING z; -- returns 'qwe'
{% endhighlight %}
