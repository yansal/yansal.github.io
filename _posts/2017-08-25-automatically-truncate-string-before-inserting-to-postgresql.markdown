---
layout: post
title:  "Automatically truncate a string before inserting to PostgreSQL"
date:   2017-08-25 00:00:00
---

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
INSERT INTO t(z) VALUES ('qwerty');
SELECT z FROM t;
{% endhighlight %}
