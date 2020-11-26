%function consolidation_semantique = consolidation_sem(phase)
%Cette fonction est conçue pour mesurer la consolidation sémantique après
%une nuit de sommeil. 
%La première condition (1)  est la phase de mémorisation. Elle consiste à présenter
%une image d'objet pairée avec un non-mot à chaque 5 secondes (nombre de paires = 20 
%(100s;1min40s). À la fin de la présentation, l'écran se ferme.
%La deuxième condition (2) est la phase test de l'expérimentation pour
%mesurer la consolidation sémantique. Un non-mot présenté lors de la séance
%de mémorisation est présenté un centre-bas de l'écran et 3 secondes plus
%tard, deux catégories sémantique (e.g. animaux apparaissent en haut à gauche et en haut
%à droite. Le participant doit appuyer sur "z" pour sélectionner la catégorie
%de gauche et "m" pour sélectionner la catégorie de droite. 
%Nettoyer le workspace et l'écran des précédentes opérations
clc;
close all;
%Renvoie un message d'erreur si la version de PsychToolBox n'est pas basée
%sur OpenGL ou si Screen() ne fonctionne pas bien
AssertOpenGL;
%Amorce le générateur de nombres aléatoires afin que tous les nombres générés soient vraiment "aléatoires"
rng('shuffle');
screens=Screen('Screens');
screenNumber=max(screens);
%Initialiser la fonction de structure pour éventuellement entrer les
%informations du participant et ses TR
info_par = struct;
%Demander numéro de participant et âge dans la Command Window
num_par = input('Numéro de participant: ');
info_par.NumParticipant = num_par;
age_par = input('Âge: ');
info_par.AgeParticipant = age_par;
%Initialiser des variables pour enregistrer par la suite si le participant
%a complété les tâches 1 et/ou 2
phase_1 = 0;
phase_2 = 0;
%Matrices contenant les noms des catégories sémantiques qui ont été
%présentées (m_cat_fam) et les catégories sémantiques qui n'ont pas été
%présentées (m_cat_unfam)
m_cat_fam = ["Bureau" "Animal" "Sport" "Plante" "Breuvage" "Fruit" "Insecte" "Légume" "Meuble"];
m_cat_unfam = ["Instrument de musique" "Véhicule" "Poisson" "Art" "Arme" "Outil" "Bâtiment" "Vêtement"];
veteur_non_mots = ["tâfethor" "sarybêt" "lenêcheg" "gecolêt" "gugareg" "labèlel" "ramomet" "gasafod" "yarevat" "lêmobur" "ceduphèc" "cosonem" "botamom" "fanisîr" "cynofép" "poçamec" "kasifoc" "cémacèm"];
image_mat = ["abeille.jpeg","bleuet.jpeg","brocheuse.jpg","brocoli.jpg","café.jpeg","canapé.jpg","carotte.jpeg","fourmi.jpeg","Girafe.jpg","kiwi.png","lion.jpeg","mojito.jpeg","stylo.jpg","succulente.jpeg","tennis.jpeg","vélo.jpeg","arbre.jpg",'table.jpg'];
%Si la condition est '1', voici ce que la fonction exécutera:
% -> Présenter une paire non-mot + image, une à la fois à l'écran
% -> Après 5 secondes, la paire non-mot + image change pour la prochaine
%%
if phase == 1
    %La valeur de phase_1 devient 1 à la place de 0 pour enregistrer le
    %fait que le participant a complété la phase 1
    phase_1 = 1;
    Screen('Preference','SkipSyncTests',1);
    white = WhiteIndex(screenNumber);
    [window,windowRect] = Screen('OpenWindow',screenNumber,[0 0 0]);
    Screen('BlendFunction', window, 'GL_SRC_ALPHA', 'GL_ONE_MINUS_SRC_ALPHA');
    %Remove Cursor from screen during experiment
    HideCursor;
    %Taille de l'écran en pixels
    [screenXpixels, screenYpixels] = Screen('WindowSize', window);
    %Centre de l'écran
    [xCenter, yCenter] = RectCenter(windowRect);
    for compteur = 1:length(image_mat)
        %Image et mot qu'on présente
        image_presented = imread(image_mat(compteur));
        word_presented = veteur_non_mots(compteur);
        %Redimensionner l'image à un ratio qui correspond au 1/3
        %de la largeur et de la hauteur de l'écran pour chacune des images.
        %Ainsi, toutes les images présentées ont la même taille par rapport
        %à l'écran.
        [oldHeight,oldWidth,oldNumberOfColorChannels] = size(image_presented);
        newWidth = screenXpixels/3;
        newHeight = screenYpixels/3;
        image_presented_croped = imresize(image_presented, [newHeight newWidth]);
        image_texture = Screen('MakeTexture',window,image_presented_croped);
        Screen('DrawTexture',window,image_texture,[],[]);
        %Partie texte de l'image: À chaque itération, on sélectionne le mot
        %à la position 'compteur' du vecteur 'vecteur_non_mot'.
        Screen('TextSize', window, 90);
        Screen('TextFont', window, 'Times'); 
        DrawFormattedText(window,num2str(word_presented),'center',screenYpixels * 0.75,white,windowRect);
        %The flip command takes everything that has been drawn and actually pushes it to the display. 
        %Without the flip command the images you draw will remain offscreen!
        Screen('Flip',window);
        %Attendre 5 secondes avant de passer à l'image suivante
        WaitSecs(5);
        compteur+1;
    end
    %It is very important to always end a script with this command if drawing is involved to send control of the screen back to MATLAB.
        Screen('CloseAll');
        
%%
elseif phase == 2
    phase_2 = 1;
    Screen('Preference','SkipSyncTests',1);
    white = WhiteIndex(screenNumber);
    [window,windowRect] = Screen('OpenWindow',screenNumber,[0 0 0]);
    Screen('BlendFunction', window, 'GL_SRC_ALPHA', 'GL_ONE_MINUS_SRC_ALPHA');
    %Remove Cursor from screen during experiment
    HideCursor;
    %Taille de l'écran en pixels
    [screenXpixels, screenYpixels] = Screen('WindowSize', window);
    %Centre de l'écran
    [xCenter, yCenter] = RectCenter(windowRect);
     %It is very important to always end a script with this command if drawing is involved to send control of the screen back to MATLAB.
        Screen('CloseAll');
else
    disp("La condition entrée n'est pas valide. Veuillez entrer 1 ou 2, dépendamment de la condition");
end


%%
presentation de la 2e phase
for compteur = 1:length(m_cat_fam)
       %générer une matrice de nombre aléatoire, soit 0 ou 1 pour déterminer la
        %condition de la matrice catégorie familière ou non familière
        mat_ordre = mat = [0,0,0,0,0,0,1,1,1,1,1,1];
        mat_mixed = shuffle(mat_ordre);
     

            cat_presented_motTest = randi([1 9]);
            motTest = vecteur_non_mots(randi(18));
            good_categorie = m_cat_fam(randi([1 9]));
            other_categorie = m_cat_fam(randi([1 9]));
            screens=Screen('Screens');
            screenNumber=max(screens);
            Screen('Preference','SkipSyncTests',1);
            white = WhiteIndex(screenNumber);
            [window,windowRect] = Screen('OpenWindow',screenNumber,[0 0 0]);
            %Screen('BlendFunction', window, 'GL_SRC_ALPHA', 'GL_ONE_MINUS_SRC_ALPHA');
            %Remove Cursor from screen during experiment
            HideCursor;
            %Taille de l'écran en pixels
            [screenXpixels, screenYpixels] = Screen('WindowSize', window);
            %Centre de l'écran
            [xCenter, yCenter] = RectCenter(windowRect);
            %Morceau pour format le font et le size, puis pour enregistrer le mot à
            %présenter (position, couleur également)
            Screen('TextSize', window, 90);
            Screen('TextFont', window, 'Times'); 
            %Non-mot présenté initialement qui est représenté à nouveau en bas au
            %centre en blanc
            DrawFormattedText(window,num2str(motTest),'center',screenYpixels * 0.75,white,windowRect);
            DrawFormattedText(window,num2str(good_categorie),screenXpixels * 0.18,screenYpixels * 0.25,white,windowRect);
            DrawFormattedText(window,num2str(other_categorie),screenXpixels * 0.68,screenYpixels * 0.25,white,windowRect);
            Screen('Flip',window);
            KbStrokeWait;
        %elseif mat_mixed(ii) == 1
           %The flip command takes everything that has been drawn and actually pushes it to the display. 
           %Without the flip command the images you draw will remain offscreen!
        %end
        compteur = compteur+1;
end
%%
%on génère une matrice de 12 mots mots sélectionnés aléatoirement parmis
%nos non-mots de départ. Ces 12 mots seront les mots présentés pour la
%phase de rappel.
for compteur = ii:12
    m_12rand = randperm(18,12);
    mots_phase2 = ["","","","","","","","","","","",""];
    for compteur = 1:length(m_12rand) %boucle for qui prend la valeur (index) de chaque position de la matrice avec des chiffres aléatoires
        %aléatoires entre 1 et 18 pour aller chercher le non-mot qui
        %correspond à la position index. Renvoie la matrice mots_phase2 qui
        %contient 12 mots aléatoires parmi les 18.
        index = m_12rand(compteur);
        mots_phase2(compteur)= vecteur_non_mots(index);
        compteur = compteur +1;
        disp(mots_phase2);
    end
    %sélectionner une autre catégorie pour la présentaion
    

 %%
%sémantique correspondante
m_catEtNonMot = ["Bureau" "Animal" "Sport" "Plante" "Breuvage" "Fruit" "Insecte" "Légume" "Meuble";...
    "lenêcheg" "yarevat" "cynofép" "fanisîr" "gugareg" "sarybêt" "tâfethor" "gecolêt" "labèlel";...
    "botamom" "ceduphèc" "poçamec" "kasifoc" "cosonem" "lêmobur" "gasafod" "ramomet" "cémacèm"];
%Martice de 12 nombres aléatoires entre 1 et 18
m_12rand = randperm(18,12);
    %Matrice qui contiendra les non-mots aux positions correspondantes de
    %la matrice m_12rand
    mots_phase2 = ["","","","","","","","","","","",""];
    %Boucle qui parcours les cases de la matrice m_12rand en insérant le
    %mot correspondant à cette position dans la matrice mots_phase2. De
    %cette façon, les mots qui seront testés seront aléatoirement définit
    %entre les participants
   for compteur = 1:length(m_12rand)
       index = m_12rand(compteur);
       mots_phase2(compteur) = vecteur_non_mots(index);
       compteur = compteur+1;
   end
   %Matrice contenant la position du mot qu'on cherche ([rangée colonne]) 
   position_mot_ran_col = [];
   %Selon le mot qu'on cherche, la catégorie correspondante à ce mot sera
   %enregistrée dans cette matrice
   categorie_mot_presented = [];
   for index = 1:length(mots_phase2)
       for rangee = 2:3
           for colonne = 1:9
               if mots_phase2(index) == m_catEtNonMot(rangee,colonne)
                   position_mot_ran_col = [rangee colonne];
                   categorie_mot_presented = m_catEtNonMot(1,colonne);
                   return
               else
                   continue
               end
           end
       end
   end








%Les deux boucles suivantes vont changer les valeurs des variables de
%complétion de tâche afin d'enregistrer si les tâches ont été complétées
if phase_1 == 1
    info_par.Phase1Completed = 'Completed';
elseif phase_1 == 0
    info_par.Phase1Completed = 'Not Completed';
end
%%
if phase_2 == 1
    info_par.Phase2Completed = 'Completed';
elseif phase_2 == 0
    info_par.Phase2Completed = 'Not Completed';
end
info_par
