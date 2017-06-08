# Prolog
Projet prolog UTBM

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
check(X, N, Y):- N > 1, M is N-1, voisin(X,Z), check(Z, M , Y),  !.

% on récupère le premier pion de la liste
sommet(X, [_,X|_]).

%on récupère la longueur de la liste
longueur([],0). % on commence par -1 pour enlever l'indice
longueur([_|L],N) :- longueur(L,N1), N is N1+1.
test(N) :- getPile(2,L), longueur(L,N).


%Pour récupérer une pile (case) avec son index getPile(Index, Liste de retour)
getPileN(I , N, L) :- pogo(L2),temp(I, N ,L, L2, L3,L2), retract(pogo(L2)) ,asserta(pogo(L3)).
getP_sub3(0, L , [] , L).
getP_sub3(N, [E|R], [E|L], L1) :- N1 is N-1, getP_sub3(N1, R, L, L1).
temp( I , N ,L, [ [I | X ] | R1 ], R,_) :- getP_sub3(N, X, L, R10), concat(R10, R1, R2), concat([[I]], R2, R),!. 
temp( I , N ,X, [ [ A | B ] | L ], [ [ A | B ] |  R ],L3) :- temp(I ,N, X ,L, R,L3), !. 

%ajouter une pile de N éléments à une pile d'indice I
setPile(I, L) :- pogo(L2),temp2(I, L, L2, L3,L2), retract(pogo(L2)) , asserta(pogo(L3)).
temp2( I ,L, [ [ I | R1 ] | R ], R5,_) :- concat(L, R1, R3), concat([I], R3, R4), concat([R4], R, R5).
temp2( I , X, [ [ A | B ] | L ], [  [ A | B ] | L2],LL) :- temp2(I ,X , L, L2,LL), !. 

getPile(X , L) :- pogo(L2), tempo(X, L, L2). tempo( I ,X, [ [I | X ] | _ ]). tempo( I , X, [ [ _ | _ ] | L ] ) :- tempo(I , X , L), !.

% prédicat qui regroupe toutes les vérifications préalables avant un de déplacer des pions
isValide(D, N) :- tour(J), getPile(D, L), sommet(J, L), longueur(L, M), M >= N, M =< N.


deplacer(D, N , A) :- getPileN(D, N, L), setPile(A, L),!.

% check+isValide
changerTour(pion(noir)) :- retract(tour(pion(noir))),asserta(tour(pion(rouge))),!.
changerTour(pion(rouge)) :- retract(tour(pion(rouge))), asserta(tour(pion(noir))),!.

%vérifie si la partie est terminé, le joueur courant doit être envoyé
fin(J) :- pogo(L), fin_sub(J, L).
fin_sub(_, []) :- !.
fin_sub(J, [ E | R ]) :- sommet(J, E), fin_sub(J, R), !.
fin_sub(J, [ E | R ]) :- longueur(E,0), fin_sub(J ,R).

jouer(D, N , A) :- check(D , N, A), isValide(D , N), deplacer(D, N , A), tour(J), not(fin(J)), changerTour(J).
%verifier si jeu est fini

concat([],L,L).
concat([E1|R1],L,[E1|R2]) :- concat(R1,L,R2).
