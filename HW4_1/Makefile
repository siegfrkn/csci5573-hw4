CC := gcc

SRC_FILES := $(wildcard *.c)
PROGS := $(patsubst %.c,%,$(SRC_FILES))

all: $(PROGS)

%.o: %.c
	$(CC) -o $@ $<

clean:
	rm -f $(PROGS)
	sudo rm -rf server_output.txt