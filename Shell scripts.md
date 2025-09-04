## script checks all remote Git branches except those containing "release" or "master" in their names.
```
branches=`git branch -r | grep -v "release\|master"`

today=`date +%s`
weekAgo=`expr $today - 60 \* 60 \* 24 \* 7`
exitCode=0

for branch in $branches
do
  comitter=`git show -s --format='%cn' $branch`
  date=`git show -s --format='%ct' $branch`
  formattedDate=`date -d @$date`
  if [ $date -le $weekAgo  ] ; then 
    echo "$formattedDate \t $comitter \t $branch"
    exitCode=1
  fi
done

exit $exitCode
```


