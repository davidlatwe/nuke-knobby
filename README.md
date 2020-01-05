<h1 align=center>nuke-knobby</h1>

<p align=center><i>Parsing back and forth between dict and node knobs in Nuke.</i></p>

<p align=center><img src="https://user-images.githubusercontent.com/3357009/71781328-688a7f00-3008-11ea-8d64-11965255f58c.png"></p>


### Features
* Convert dict data into Nuke node knobs.
* Extract knob values back into dict.
* Won't have duplicated knobs in multiple imprints to same node.
* Knob name will be prefixed with parent tab name.

### Examples

#### Imprint

Parsing dict into knobs under a tab

```python
import nuke
from collections import OrderedDict
from knobby import util

node = nuke.createNode("NoOp")
data = OrderedDict([
    # Regular type of attributes
    ("options", ["x", "y", "z"]),
    ("enable", True),
    ("width", 100.15),
    ("count", 5),

    # Manual nice knob naming
    (("speach", "My Speach"), "Hello World!"),

    # Creating non-default imprint type of knob
    ("filePath", util.Knobby("File_Knob", "/file/path")),
    (("divider1", ""), util.Knobby("Text_Knob", "")),

    # dict type will be created as knob group
    (
        "KnobGroup",
        OrderedDict([
            ("knob1", 5),
            ("knob2", "hello"),
            ("knob3", ["a", "b"]),
        ])
    ),

    (("divider2", ""), util.Knobby("Text_Knob", "")),

    # Nested dict will be created as tab group
    (
        "TabGroup",
        OrderedDict([
            ("tab1", {"count": 5}),
            ("tab2", {"isGood": True}),
            ("tab3", {"direction": ["Left", "Right"]}),
        ]),
    ),
])

util.imprint(node, data, tab="Demo")

```

#### Read

Parsing user knobs with filter and put into dict

```python
import nuke
from knobby import util

node = nuke.createNode("NoOp")
util.imprint(node, {"value": 5}, tab="this")
util.imprint(node, {"value": 2}, tab="that")

def get_value(name):
    tab, name, *args = name.split(":")[:2]
    return name if tab == "this" and name == "value" else None

heights = util.read(node, filter=get_value)
assert heights == {"value": 5}

```

#### Mold

Parsing user knobs by tab and put into dict with tab hierarchy preserved

```python
import nuke
from knobby import util

node = nuke.createNode("NoOp")
data = {
    "data": {
        "david": {
            "age": 31,
            "height": 172.9,
            "weight": 78,
        },
        "tom": {
            "age": 27,
            "height": 165.9,
            "weight": 68,
        },
    }
}
util.imprint(node, data, tab="person")

mold = util.mold(node)
assert mold == {"person": data}

mold = util.mold(node, tab="person")
assert mold == data

mold = util.mold(node, tab="person:data:tom")
assert mold == {
    "age": 27,
    "height": 165.9,
    "weight": 68,
}

```
