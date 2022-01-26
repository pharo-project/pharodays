Instructions to generate HTML schedule from CSV file:

## Load NeoCsv and Mustache
```smalltalk
Metacello new
	repository: 'github://svenvc/NeoCSV/repository';
	baseline: 'NeoCSV';
	load.
Metacello new
	repository: 'github://noha/mustache/repository';
	baseline: 'Mustache';
	load.
```

## Generate HTML
We first read the CSV file and then generate HTML chunks we aggregate into an ouput file.
```smalltalk
pharodaysRepo := FileLocator home / 'dev' / 'rmod' / 'pharodays'.
schedules := pharodaysRepo / 'templates' / 'schedule.csv' 
	readStreamDo: [ :readStream |
		(NeoCSVReader on: readStream)
			separator: $;;
			skipHeader;
			emptyFieldValue: '';
			addField;
			addField;
			addField;
			addField;
			upToEnd ].
		
scheduleDetailTemplate := (pharodaysRepo / 'templates' / 'schedule-detail.html') asFileReference contents asMustacheTemplate.

fullSchedule := ''.
schedules do: [ :schedule | | html |
	html := scheduleDetailTemplate value: { 
		'TIME' -> schedule first trim. 
		'SPEAKER' -> schedule second trim.
		'SUBJECT' -> schedule third trim.
		'ROOM' -> schedule fourth trim} asDictionary.
	fullSchedule := fullSchedule, html. ].
scheduleDetailTemplatefullShedule :=  pharodaysRepo / 'templates' / 'shedule-workshop.html'.
scheduleDetailTemplatefullShedule ensureDelete.
scheduleDetailTemplatefullShedule writeStreamDo: [ :s | s nextPutAll: fullSchedule ].
```
Then, you just neeed to copy/paste the generated HTML into the right place (index.html or workshop.html/
