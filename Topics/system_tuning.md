## system tuning
tuned - alapban nincs elindítva (Almában igen)
  > systemctl enable --now tuned

kezelés: tuned-adm paranccsal
tuned-adm active  -- épp aktuális profil
tuned-adm list    -- összes profil listázása
tuned-adm profile -- profil alkalmazása -->  TÖBB profilt is lehet EGYSZERRE
tuned-adm off


több profil élesíthető EGYSZERRE