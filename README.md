# თაღლითური ტრანზაქციის პროგნოზირება
##### Kaggle-ის IEEE-CIS Fraud Detection კონკურსის მიზანია განსაზღვრა, არის თუ არა ტრანზაქცია თაღლითური სხვადასხვა მახასიათებლებზე დაყრდნობით, როგორიცაა ტრანზაქციის თანხა, მომხმარებლის მოწყობილობა და სხვ. მონაცემთა ნაკრები შედგება ბევრი მრავალფეროვანი ცვლადისგან, რომლებიც საჭიროებენ წინასწარ დამუშავებას: cleaning, feature engineering და feature selection, რათა მოდელს მიეცეს შესაძლებლობა, რაც შეიძლება ზუსტად ამოიცნოს თაღლითური ტრანზაქციები.

# ჩემი მიდგომა

##### Cleaning: წავშალე ისეთი სვეტები, რომელთაც აკლდა მონაცემების დიდი წილი (მეტია მითითებულ threshold-ზე, მაგალითად 70%-ზე). ეს threshold არის ჰიპერპარამეტრი, რომელიც ილოგება MLflowze.
##### Feature Engineering: რიცხვით სვეტებში NaN მნიშვნელობები შევცვალე მედიანით, ხოლო კატეგორიულში — მოდით (mode). რიცხვითი ცვლადები დავასკალირე StandardScaler-ით, ხოლო კატეგორიული ცვლადები შევცვალე OneHotEncoder-ის გამოყენებით.
##### დამატებით გავაკეთე ახალი feature: "TransactionAmt_log" (ტრანზაქციის თანხის ლოგარითმი), რათა ტრანზაქციის თანხის გაწელილი განაწილება გადამეტანა უფრო ნორმალურ ფორმაზე. ეს ეტაპიც შევიტანე მთლიან pipeline-ში.
##### Feature Selection: SelectKBest-ის მეშვეობით ავარჩიე საუკეთესო რიცხვითი სვეტები f_classif შეფასების მიხედვით.
##### Training: გამოვიყენე სხვადასხვა მოდელები: Logistic Regression, Random Forest, AdaBoost, XGBoost. თითოეულზე გავაკეთე GridSearchCV ჰიპერპარამეტრების ოპტიმიზაცია.
##### შეფასება: გამოვიყენე AUC (Area Under the Curve) მეტრიკა.
##### MLflow-ს საშუალებით დავტრეკე ყველა ნაბიჯი: cleaning, feature engineering, feature selection და model training. დავლოგე მეტრიკები და საუკეთესო მოდელები დავარეგისტრირე.

# რეპოზიტორიის სტრუქტურა
- ##### **notebooks** დირექტორია:
  - ##### **model_experiment_XGBoost.ipynb**, **model_experiment_adaboost.ipynb** და სხვ. - ცალკე მოდელის ექსპერიმენტის ფაილი.
  - ##### **model_inference.ipynb** - მოდელის ჩამოტვირთვა MLflow-დან და სატესტო მონაცემებზე პროგნოზირება.

# Feature Engineering
##### რიცხვით და კატეგორიულ სვეტებზე გამოვიყენე შესაბამისი პრეპროცესორები (StandardScaler და OneHotEncoder), მანამდე კი არარსებული მონაცემები რიცხვით სტრიქონებში მედიანით, ხოლო კატეგორიულში მოდით შევცვალე.
##### დავამატე ახალი ფიჩერი: "TransactionAmt_log".
##### მთელი Feature Engineering პროცესი შესრულებულია scikit-learn-ის Pipeline-ის შიგნით, რათა მოდელის ჩატვირთვისას აღარ იყოს საჭირო ცალკე პრეპროცესინგი.
##### Cleaning ფაზაში წავშალე ისეთი სვეტები, რომლებსაც threshold-ზე მეტი NaN ჰქონდათ.

# Feature Selection
##### SelectKBest-ის გამოყენებით ავარჩიე უმნიშვნელოვანესი რიცხვითი სვეტები, ხოლო კატეგორიული სვეტები OneHotEncoding-ის შემდეგ მთლიანად შევიტანე.

# Training
##### გამოვიყენე სხვადასხვა მოდელი (Random Forest, Logistic Regression, XGBoost, AdaBoost) და თითოეულზე ვაწარმოე ჰიპერპარამეტრების ოპტიმიზაცია GridSearchCV-ის საშუალებით.
##### მოდელები შევაფასე AUC მეტრიკით როგორც სატრენინგო, ასევე ვალიდაციის სეტებზე.
##### საუკეთესო შედეგი მიღწეულია XGBoost მოდელზე.

# MLflow Tracking
##### [ექსპერიმენტების ბმული](https://dagshub.com/gnada22/ml_assignment_2.mlflow)
#### ჩაწერილი მეტრიკები
- ##### drop_threshold - რა ზღვარი გამოვიყენეთ NaN სვეტების წასაშლელად.
- ##### num_cols_dropped - რამდენი სვეტი წაიშალა.
- ##### initial_missing_ratio - რამდენი პროცენტია მთლიანად მონაცემებში NaN.
- ##### new_features - დამატებული feature-ები.
- ##### num_selected_features - Feature Selection-ის შედეგად არჩეული სვეტების რაოდენობა.
- ##### selected_features - არჩეული სვეტები.
- ##### best_params - საუკეთესო ჰიპერპარამეტრების მნიშვნელობები GridSearchCV-ის შედეგად.
- ##### train_auc - AUC შეფასება სატრენინგო მონაცემებზე.
- ##### valid_auc - AUC შეფასება ვალიდაციის მონაცემებზე.

# საუკეთესო მოდელის შედეგები
- ##### Train AUC: 0.8502943503611089 (XGBoost-ზე)
- ##### Validation AUC: 0.8469625891084587 (XGBoost-ზე)
- ##### Public Score: 0.871392 (XGBoost-ზე)
- ##### Private Score: 0.834872 (XGBoost-ზე)
