# Architecture Proposal

## Cloning this repo

Clone with the `--recursive` option to checkout submodules automatically:

```bash
git clone --recursive git@github.com:Ride2Rail/offer-enhancer.git offer-enhancer
```

## Running

```bash
cd offer-enhancer/
docker-compose build
docker-compose up
```

## Example request

```bash
$ curl -s \
       -F "offers=\"$(base64 examples/trias_example.xml)\"" \
       -X POST \
       http://localhost:5000/categorize | \
  jq .
{
  "categorization": {
    "C-1": {
      "categories": {
        "Multitasking": 0.388,
        "Comfortable": 0.395,
        "Quick": 0.583
      }
    },
    "C-2": {
      "categories": {
        "Quick": 0.529,
        "Door-to-door": 0.594,
        "Comfortable": 0.413
      }
    },
    "C-4": {
      "categories": {
        "Cheap": 0.764,
        "Social": 0.617,
        "Panoramic": 0.715,
        "Multitasking": 0.624
      }
    },
    "C-0": {
      "categories": {
        "Envirnmentally friendly": 0.6,
        "Social": 0.557
      }
    }
  }
}
```

## Run docker manually

```bash
docker network create r2r-net;

docker run -it \
           --rm \
           --name r2r_api \
           --network r2r-net \
           --network-alias api \
           -p 5000:80 \
           -v $(pwd)/app:/app \
             r2r/api:latest /start-reload.sh

docker run -it \
           --rm \
           --name r2r_categorizer \
           --network r2r-net \
           --network-alias categorizer \
             r2r/categorizer:latest
```

## Troubleshooting

* `docker-compose build` fails to build, with error:

```bash
ERROR: Service 'categorizer' failed to build : COPY failed: stat /[...]/proto/r2r: no such file or directory
```

This is due to the fact that, when cloning the repo, the submodules have not been cloned recursively. To fix it, run from the repository base directory the following:

```bash
git submodule update --init --recursive
```
