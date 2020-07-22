# To-do list

## General

- [ ] Come up with an actual name for the project
- [ ] Move some of these To-do items to github issues so that we can reference them
- [ ] Make the Noty notifactions have a theme that matches the rest of the app (both the landing page and front-end use the noty.js library to display alerts, however the default colors on these look weird)
- [ ] Create actual `unittest` test cases for most server functionality
  - [ ] Use the existing master branch to get expected responses from various functions
  - [ ] Convert the existing cases to use `unittest`
- [ ] Rebase/merge the feature/cycle branch
- [ ] Create a script to automatically calculate the bounds for each dataset so that we can include those in the entries in `datasets.json`.
  - Without the bounds of a tileLayer, leaflet will try to grab imagery for the entire viewport (and will get many 404 errors in return). This is annoying. 

## Landing page

- [ ] Dummy password authentication.
  - [ ] The landing page should have a hardcoded password prompt that you must pass in order to use the page. Passing this prompt should set a 1-day cookie that bypasses it.
- [ ] The "current list of checkpoints" should have icons that allow them to be renamed or deleted.
- [ ] When a dataset/model/checkpoint is selected then we should display useful information about it. Brainstorm what these should be.
- [ ] If you quickly double click on the #dataset-header, #model-header, or #checkpoint-header areas then the state of the component won't match what is shown. This needs to be fixed. See TODO in the code for details.

## Front-end

- [ ] Show download processing status on the front-end
  - [ ] When you press download there should be some sort of status indiator on the front-end that changes when the download is done / fails.
  - [ ] (Potentially) The front-end should not wait for results on the same HTTP request that a download was initiated on and should instead poll for results. 
- [ ] Session poll thread on the front-end
  - [ ] Every 10ish seconds the front-end should poll an endpoint to ask what the status of its session is.
  - [ ] The status should be displayed somewhere on the page
  - [ ] If the session has died then some blocking indication should be given
- [ ] Add ability for multiple named basemaps to be passed through the entries in `datasets.json`
  - [ ] The front-end should display all basemaps for the selected dataset.
  - [ ] Add a hotkey to switch between the custom basemap layers
- [ ] The front-end does not need to call `get_input()` or display the image/model results in the top-right corner

## Server

- [ ] Create small debug page (e.g. `/sessions.html`) that shows a list of the active sessions
- [ ] Make the Session object start ModelSessions with kwargs from `models.json`.

## Documentation

- [ ] Create an updated video demo / instructional video 
- [ ] Document the existing API exposed by `server.py`, create `API.md`
- [ ] Create simple instructions for how to use the tool to include in `README.md`
- [ ] Document the DataLoaderAbstract interface
- [x] Document the ModelSessionAbstract interface
- [ ] Document the process by which users can train / add an unsupervised model using the `training/train_autoencoder.py` script.

## Larger projects that span multiple pieces of the codebase
- [ ] Total rework of model saving and loading. Currently the tool generates a custom link that a model can be restored at however this is brittle and unintuitive to users.
  - [x] Rename ServerModelsAbstract to ModelSessionAbstract throughout.
    - [x] Clean up (remove NAIP references) and re-document the interface
    - [x] Add `save_state_to()` and `load_state_from()` methods to the interface. Now, "ServerModels" will be responsible for serializing their state to a directory.
    - [ ] Implement `save_state_to()` and `load_state_from()` in the keras ModelSession class
  - [ ] Add a checkpoint model button to the front-end.
    - [ ] This should prompt for a checkpoint name.
    - [ ] This should save the model to disk.
    - [ ] This should save an entry in a checkpoint database.
  - [ ] The landing page should have an additional section that shows available checkpoints for each (dataset, model) pair.
    - The expected flow is: "user selects a dataset" --> "valid list of models are displayed" --> "user selects a model" --> "current list of checkpoints are displayed" --> "user selects a checkpoint or 'new'" --> "start server button is enabled"
    - [x] Add "valid_models" key to each dataset that is a list of acceptable models.
    - [x] Additionally, the landing page should give the option to start from an empty model.
  - [ ] Add instructions for install/running a local redis server to store checkpoints in.

- [ ] The commnication between `server.py` and `worker.py` needs to be re-worked.
  - Currently `server.py` will spawn an instance of `worker.py` for every "session" that is created through the front-end. Communication between the server and the worker are handled by `rpyc` RPC calls. In long running computations on the worker (e.g. running a model over a tile), the connection will time-out. Also, the RPC call seems to incur a significant overhead when passing large arrays (e.g. a 7000x7000x20 numpy array).
  - Celery with a Redis backend might be a good solution here.
  - [ ] Move this to a GitHub issue

- [ ] The `pred_tile` code path should return the class statistics directly instead of saving to txt
  - [ ] Show class statistics when we click on a polygon in the front-end

- [ ] Re-visit the ability to draw / run inference over polygons
  - Currently the workflows for interacting with "user added polygons" and "dataset polygons" are totally separate which is confusing for everyone. These should be merged.
  - [ ] When the user firsts adds a polygon there should be a new "User layer" that is added to the list of current zones in the bottom left
  - [ ] The user is free to add, delete, change polygons in this layer
  - [ ] The user should be able to download this layer as a geojson
  - [ ] The user should be able to click on any current polygon in this zone and run inference over it by pressing "Download" like usual

- [ ] Re-visit the `train_autoencoder.py` script
  - [ ] Rename to something more appropriate
  - [ ] Convert to PyTorch.
  - [ ] After fitting the initial KMeans model do not generate a giant in-memory dataset. Instead, sample on-the-fly during a training loop