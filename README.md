# Flask bundle

Support for Flask in [applauncher](https://github.com/maxpowel/applauncher)

Install: pip install flask_applauncher_bundle

## Configuration example

```yml
flask:
  use_debugger: True
  port: {flask_port}
  host: {flask_host}
```

## Usage

Flask can load resources by a modular way called Blueprints. Your application only has
to provide a blueprint (with your controllers). Lets see how to do this.

```python
from flask import Blueprint, jsonify
import zope.event
import applauncher.kernel
import inject
from flask_bundle import ApiBlueprints

my_blueprint = Blueprint(
    'myApp', __name__
)

@my_blueprint.route("/lol", methods=['GET'])
def my_app_get_index():
    return jsonify({"foo": "bar"})
    
# The bundle
class WebBundle(object):
    def __init__(self):
        # I dont need any configuration so I simply ignore the field config_mapping
        
        # We need to add our blueprint when the dependency injection is ready so
        # we need to listen for this event
        zope.event.subscribers.append(self.event_listener)

    def event_listener(self, event):
        if isinstance(event, applauncher.kernel.InjectorReadyEvent):
            # When the injection is ready, we fetch the blueprints array
            # provided by the FlaskBundle
            bp = inject.instance(ApiBlueprints)
            bp.append(my_blueprint)
            # that's all, later the FlaskBundle will configure this blueprint
```


For more information about creating webs with flask just check the flask
documentation

