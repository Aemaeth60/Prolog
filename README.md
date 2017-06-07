# Prolog
Projet prolog UTBM

%Permet de modifier les prédicats
:- dynamic(pogo/1).
:- dynamic(tour/1).

pion(rouge).
pion(noir).

tour(pion(noir)).

%Tableau de jeu

pogo( 
	  [
	  [1,pion(rouge),pion(rouge)] , [2,pion(rouge),pion(rouge)], [3,pion(rouge),pion(rouge)],
      [4] , [5], [6],
      [7,pion(noir),pion(noir)] , [8,pion(noir),pion(noir)], [9,pion(noir),pion(noir)] 
	  ]
).

%Jeu

voisin(1,2).
voisin(1,4).

voisin(2,1).
voisin(2,3).
voisin(2,5).

voisin(3,2).
voisin(3,6).

voisin(4,1).
voisin(4,5).
voisin(4,7).

voisin(5,2).
voisin(5,4).
voisin(5,6).
voisin(5,8).

voisin(6,3).
voisin(6,5).
voisin(6,9).

voisin(7,8).
voisin(7,4).

voisin(8,7).
voisin(8,5).
voisin(8,9).

voisin(9,6).
voisin(9,8).

%On regarde si le déplacement est possible
check(X, 1, Y) :- voisin(X, Y), !.
check(X, N, Y):- N > 1, M is N-1, check(Z, M , Y), voisin(X,Z), !.

% on récupère le premier pion de la liste
sommet(X, [_,X|_]).

%on récupère la longueur de la liste
longueur([],0). % on commence par -1 pour enlever l'indice
longueur([_|L],N) :- longueur(L,N1), N is N1+1.
test(N) :- getPile(2,L), longueur(L,N).


%Pour récupérer une pile (case) avec son index getPile(Index, Liste de retour)
getPile(X , L) :- pogo(L2), temp(X, L, L2).
temp( I ,X, [ [I | X ] | _ ]).
temp( I , X, [ [ _ | _ ] | L ] ) :- temp(I , X , L), !. 

% prédicat qui regroupe toutes les vérifications préalables avant un de déplacer des pions
isValide(J, A, N) :- tour(J), getPile(A, L), sommet(J, L), longueur(L, M), M >= N.

%deplacement
% check+isValide

%verifier si jeu est fini
