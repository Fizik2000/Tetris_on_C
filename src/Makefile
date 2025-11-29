CFLAGS = -Wall -Wextra -Werror -std=c11 -g # -pedantic -fsanitize=address -fsanitize=undefined
NCURSESFLAGS = -lncurses
CHECKFLAGS = -lcheck -lm -lpthread -lrt -lsubunit
COMPILATOR = gcc

.PHONY: dvi

# clean, dist.
all: install play

install_library:
	$(COMPILATOR) $(CFLAGS) -c brick_game/tetris/*.c $(NCURSESFLAGS)
	mkdir -p build/tetris_library/
	cp *.o build/tetris_library/
	cd build/ && touch high_score.txt
	echo 0 > build/high_score.txt
	rm -rf *.o
	ar cr build/tetris_library.a build/tetris_library/*.o

install_interface:
	$(COMPILATOR) $(CFLAGS) -c brick_game/tetris/*.c gui/cli/*.c $(NCURSESFLAGS)
	mkdir -p build/tetris_interface
	cp *.o build/tetris_interface
	rm -rf *.o
	ar cr build/tetris_interface.a build/tetris_interface/*.o


install: install_library install_interface
	$(COMPILATOR) $(CFLAGS) tetris.c build/tetris_interface.a -o build/tetris.o $(NCURSESFLAGS)

play:
	./build/tetris.o

uninstall:
	rm -rf build

clean: uninstall
	rm -rf tests
	rm -rf dvi/documantation.aux
	rm -rf dvi/documantation.log
	rm -rf dvi/documantation.pdf
	rm -rf tetris-1-0.tar.gz
	
dvi: dvi/documantation.tex
	xelatex -output-directory=dvi dvi/documantation.tex

open_dvi:
	evince dvi/documantation.pdf

dist:
	mkdir -p for_dist
	cp Makefile  brick_game/tetris/*.c brick_game/tetris/*.h gui/cli/*.c gui/cli/*.h for_dist
	tar -czvf tetris-1-0.tar.gz for_dist
	rm -rf for_dist

clang_check:
	clang-format -n tetris.c brick_game/tetris/*.c brick_game/tetris/*.h gui/cli/*.c gui/cli/*.h

clang:
	clang-format -i tetris.c brick_game/tetris/*.c brick_game/tetris/*.h gui/cli/*.c gui/cli/*.h

test: install_library
	mkdir -p tests
	checkmk tests.check > tests/tests.c
	clang-format -i tests/tests.c
	$(COMPILATOR) $(CFLAGS) tests/tests.c build/tetris_library.a -o tests/tests.o $(NCURSESFLAGS) $(CHECKFLAGS)
#valgrind --tool=memcheck --leak-check=yes --track-origins=yes
	./tests/tests.o

gcov_report: install_library
	mkdir -p tests
	checkmk clean_mode=1 tests.check > tests/tests.c
	clang-format -i tests/tests.c
	$(COMPILATOR) $(CFLAGS) --coverage tests/tests.c brick_game/tetris/*.c -o tests/tests.o $(NCURSESFLAGS) $(CHECKFLAGS)
	./tests/tests.o
	cd tests && lcov -t "Test_report" -o test_report.info -c -d .
	cd tests && lcov --remove test_report.info 'tests.c' --output-file test_report.info
