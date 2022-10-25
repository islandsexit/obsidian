[[Android]]

ViewBinding нужен для того, чтобы не писать вручную `view.findViewById()`

Вначале в `gradle.build` нужно указать 

`android{
	`...
	`viewBinding {`
		`enabled =true`
		`}`
`}`


Потом 

![[photo_2022-10-01_15-01-52.jpg]]