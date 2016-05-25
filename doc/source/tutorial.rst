Tutorial
========

Filtering and mangling
----------------------

The filter spec is based on `MongoDB <https://docs.mongodb.com/manual/tutorial/query-documents/>`_:

.. code-block:: python

   from link.utils.filter import Filter

   spec = {
       # ...
   }
   f = Filter(spec)

   if f.match(dictionary):
       print 'match'

And the mangling spec is also based on `MongoDB <https://docs.mongodb.com/manual/tutorial/modify-documents/>`_:

.. code-block:: python

   from link.utils.filter import Mangle

   spec = {
       # ...
   }

   m = Mangle(spec)
   newdict = m(dictionary)

Logging
-------

The logging module sets a new logging class, it just needs to be imported.
Hopefully, the library ``b3j0f.conf`` provides a way to do that automatically:

Edit the file ``$B3J0F_CONF_DIR/b3j0fconf-configurable.conf``:

.. code-block:: javascript

   {
     "CONFIGURABLE": {
       "modules": [
         "link.utils.logging"
       ]
     }
   }

Then, configure the new logging class by editing the file ``$B3J0F_CONF_DIR/link/utils/logging.cong``:

.. code-block:: javascript

   {
     "LOGGING": {
       "log_format": "[%(asctime)s] [%(levelname)s] [%(name)s] %(message)s",
       "log_level": "INFO",
       "log_filter": {
         "name": {"$regex": "^link\..*"}
       }
     }
   }

*NB:* The ``log_filter`` option is a MongoDB filter used to filter logging records.

Finally, just use the ``logging`` module as usual.

Logging Records
---------------

For more information about what a logging record is, `click here <https://docs.python.org/library/logging.html#logrecord-objects>`_.

A record is transformed into a ``dict`` for filtering, it validates the following `JSON schema <http://json-schema.org/>`_:

.. code-block:: python

   {
     "$schema": "http://json-schema.org/schema#",

     "type": "object",
     "properties": {
       "name": {
         "title": "record.name",
         "description": "Logging record name attribute",
         "type": "string"
       },
       "level": {
         "title": "record.level",
         "description": "Logging record level attribute",
         "type": "integer"
       },
       "pathname": {
         "title": "record.pathname",
         "description": "Logging record pathname attribute",
         "type": "string"
       },
       "lineno": {
         "title": "record.lineno",
         "description": "Logging record lineno attribute",
         "type": "integer"
       },
       "msg": {
         "title": "record.msg % record.args",
         "description": "Logging record msg attribute formatted with args attribute",
         "type": "string"
       },
       "func": {
         "title": "record.func",
         "description": "Logging record func attribute",
         "type": "string"
       },
       "sinfo": {
         "title": "record.sinfo",
         "description": "Logging record sinfo attribute (null if Python 2)",
         "$oneOf": [
            {"type": "string"},
            {"type": "null"}
         ]
       },
       "exc_info": {
         "title": "record.exc_info",
         "description": "Logging record exc_info attribute",
         "$oneOf": [
           {
             "type": "object",
             "properties": {
               "type": {
                 "title": "record.exc_info[0].__name__",
                 "description": "Exception's name",
                 "type": "string"
               },
               "msg": {
                 "title": "str(record.exc_info[1])",
                 "description": "Exception's value",
                 "type": "string"
               },
               "traceback": {
                 "title": "''.join(traceback.format_tb(record.exc_info[2]))",
                 "description": "Exception's traceback",
                 "type": "string"
               }
             }
           },
           {"type": "null"}
         ]
       }
     }
   }
