# Introduction #

The current source code build of beetFs requires two modifications to beets itself. These modifications are trivial and are listed below. The plan is to integrate these changes into beets, but for now you'll have to alter beets yourself and then re-install.


# Details #

All changes are in library.py.

**First change**
_Add 'id' to ITEM\_DEFAULT\_FIELDS near the top of library.py_

Old: ITEM\_DEFAULT\_FIELDS = ALBUM\_DEFAULT\_FIELDS + ('title', 'comments')

New: ITEM\_DEFAULT\_FIELDS = ALBUM\_DEFAULT\_FIELDS + ('title', 'comments', 'id')

**Second change**
_Add the following method directly below the comment "# Browsing." in library.py_

```
    def get_path(self, id, query=None):
        query = self._get_query(query, ITEM_DEFAULT_FIELDS)
        if id is not None:
            # "Add" the artist to the query.
            query = AndQuery((query, MatchQuery('id', id)))
        where, subvals = query.clause()
        sql = "SELECT DISTINCT path FROM items " + \
              "WHERE " + where
        c = self.conn.execute(sql, subvals)
        return [(res[0]) for res in c.fetchall()]
```