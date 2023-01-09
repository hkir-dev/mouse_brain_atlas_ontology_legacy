### This makefile requires python3 & pip3 mapped as such

JOBS = 1 # 17 10 16 8
PRODUCTS = ../../mba.ofn ../../mba.owl

ROBOT = robot
LINKML = linkml-data2owl

URIBASE = http://purl.obolibrary.org/obo

STRUCTURE_GRAPHS = $(patsubst %, sources/%.json, $(JOBS))
ALL_GRAPH_ONTOLOGIES = $(patsubst sources/%.json,%.ofn,$(STRUCTURE_GRAPHS))


all: dependencies $(PRODUCTS)

# Installing depedencies so it can run in ODK container
.PHONY: dependencies
dependencies:
	pip3 install -r ../../requirements.txt

sources/%.json:
	curl -o $@ $(subst %,$(subst sources/,,$@),"http://api.brain-map.org/api/v2/structure_graph_download/%")

../linkml/data/template_%.tsv: $(STRUCTURE_GRAPHS)
	python3 ../scripts/structure_graph_template.py -i $< -o $@

%.ofn: ../linkml/data/template_%.tsv
	$(LINKML) -C Class -s ../linkml/structure_graph_schema.yaml $< -o $@

../../mba.ofn: $(ALL_GRAPH_ONTOLOGIES)
	cp 1.ofn $@
.PRECIOUS: ../../mba.ofn

../../mba.owl: ../../mba.ofn
	$(ROBOT) convert --input $< -f owl -o $@
.PRECIOUS: ../../mba.owl