astmonkey
---------

``astmonkey`` is a set of tools to work with Python AST.

Installation
------------

The easiest way to install ``astmonkey`` is clone this repository and use ``setup.py``:

::

    $ hg clone https://khalas@bitbucket.org/khalas/astmonkey
    $ cd astmonkey
    $ python setup.py install

``astmonkey.transformers.ParentNodeTransformer``
------------------------------------------------

This transformer adds few fields to every node in AST:

* ``parent`` - link to parent node,
* ``parents`` - list of all parents (only ``ast.expr_context`` nodes have more than one parent node, in other causes this is one-element list),
* ``parent_field`` - name of field in parent node including child node,
* ``parent_field_index`` - parent node field index, if it is a list.

Example usage:

::
    
    import ast
    from astmonkey import transformers

    node = ast.parse('x = 1')
    node = transformers.ParentNodeTransformer().visit(node)

    assert(node == node.body[0].parent)
    assert(node.body[0].parent_field == 'body')
    assert(node.body[0].parent_field_index == 0)

``astmonkey.visitors.GraphNodeVisitor``
---------------------------------------

This visitor creates Graphviz graph from Python AST (via ``pydot``). Before you use 
``astmonkey.visitors.GraphNodeVisitor`` you need to add parents links to tree nodes 
(with ``astmonkey.transformers.ParentNodeTransformer``).

Example usage:

::

    import ast
    from astmonkey import visitors, transformers

    node = ast.parse('def foo(x):\n\treturn x + 1')
    node = transformers.ParentNodeTransformer().visit(node)
    visitor = visitors.GraphNodeVisitor()
    visitor.visit(node)

    visitor.graph.write_png('graph.png')

Produced ``graph.png``:

.. image:: https://bitbucket.org/khalas/astmonkey/raw/default/examples/graph.png

``astmonkey.utils.is_docstring``
--------------------------------

This routine checks if target node is a docstring. Before you use 
``astmonkey.utils.is_docstring`` you need to add parents links to tree nodes 
(with ``astmonkey.transformers.ParentNodeTransformer``).

Example usage:

::

    import ast
    from astmonkey import utils, transformers

    node = ast.parse('def foo(x):\n\t"""doc"""')
    node = transformers.ParentNodeTransformer().visit(node)

    docstring_node = node.body[0].body[0].value
    assert(not utils.is_docstring(node))
    assert(utils.is_docstring(docstring_node))
