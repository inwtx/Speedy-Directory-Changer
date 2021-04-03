# Speedy-Directory-Changer
v1.1
```
#!/bin/bash
#
# CD.sh
# This script makes it easier to switch to sub-directories.
# It works in conjunction with the .bashrc file. It can only list
# up to 34 directories.  Place this script contents into a file
# called 'CD.sh' and make it executable. It should reside in any
# folder that contains subfolders. You can setup 'CD.sh' by running
# 'CD.sh y' first to load all subfolders (up to 34) within the base
# folder that you have placed CD.sh in. Then run './CD.sh' and you
# will get a list of subfolders that you can change to.
#
# The CD.sh.folders contain the folder list.  Format will be as
# below. You can remove folder lines that you will never need and
# they will not list. You can also add subfolders to CD.sh.folders
# if needed. If you need to go to a sub folder within a sub folder
# (example './folder1/folder2/'), you can manually put the line in
# the 'CD.sh.folders' file.

# 'CD.sh.folders' example entries:
#./folder1/
#./folder1/folder2/
#./folder2//
#./folder3/
# ...

# Place these next 6 lines into the respective root and/or user's .bashrc file.
# alias cdd='
# ./CD.sh
# varub1="$(<CD.sh.txt)"
# cd $(pwd)/$varub1
# varub1=""
# '

# To execute the script, either type './CD.sh' or 'cdd' and hit enter.


#--------------------------------------------------------------------------------

filePath=${0%/*}
cat /dev/null > $filePath/CD.sh.txt

## Build folder list
if [[ $1 == "y" ]]; then
   echo "$(ls --color=never -d */)" > $filePath/CD.sh.folders
   sed -i 's/^/\.\//' $filePath/CD.sh.folders
fi

depth=1
a=0
key=0
level="123456789abcdefghijklmnopqrstuvwxyz"  # 35 posibilities

if [ ! -e $filePath/CD.sh.folders ]; then
   echo "" > $filePath/CD.sh.folders
fi

function DelFiles(){      # pause function
     rm $filePath/CD.sh.txt2 2> /dev/null
}

function pause1(){      # pause function
   read -n1 -r -p "Select the directory to change to: " key

   echo ""
}

function listholdfolders(){
   echo "" > $filePath/CD.sh.txt2  # clear file

   while read lineCD1; do
      if [[ $lineCD1 == "." ]] || [[ $lineCD1 =~ "#" ]]; then  # bypass 1st empty line
         continue
      fi

##      if [[ ! $varCD == "" ]] && [[ $lineCD1 =~ "$varCD" ]]; then
      if [[ ! $varCD == "" ]] && [[ $(echo "$lineCD1" | grep -o '/' | wc -l) -gt 1 ]]; then  # indent if more than one /
#         varcdspc="   "
         varcdspc=" "
         else
         varcdspc=" "
         varCD=$lineCD1
      fi

      echo ${level:a:1}"$varcdspc"$lineCD1 >> $filePath/CD.sh.txt2
      ((a++))

      if [[ $a -gt 34 ]]; then  # can only handle 34 (1-9 and a-z)
         break
      fi
   done< $filePath/CD.sh.folders
}

#- Processing ------------------------------------------

sed -i '$!N; /^\(.*\)\n\1$/!P; D' CD.sh.folders  # del dup lines

listholdfolders  # list already added folders

sed -i '/^$/d' $filePath/CD.sh.txt2  # del any blank lines
cat $filePath/CD.sh.txt2  # display folders with pre nums

pause1  # get key

while read lineCD1; do
      if [[ $key == ${lineCD1:0:1} ]]; then
         output="${lineCD1#*/}"
         echo ./"$output" > $filePath/CD.sh.txt
         break
      fi
done< $filePath/CD.sh.txt2


ls -a --color=always ./$output


DelFiles

exit 0
```
