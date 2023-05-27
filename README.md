import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { FontAwesome } from '@expo/vector-icons';
import { useFonts } from 'expo-font';
import AsyncStorage from '@react-native-async-storage/async-storage';

import Header from './components/Header';
import SearchScreen from './screens/SearchScreen';
import UserDetailsScreen from './screens/UserDetailsScreen';
import FavoritesScreen from './screens/FavoritesScreen';
import { colors } from './constants';

const Tab = createBottomTabNavigator();

export default function App() {
  const [fontsLoaded] = useFonts({
    'FontAwesome': require('@expo/vector-icons/fonts/FontAwesome.ttf'),
  });

  if (!fontsLoaded) {
    return null;
  }

  return (
    <NavigationContainer>
      <Header />

      <Tab.Navigator
        tabBarOptions={{
          style: { backgroundColor: colors.TabBar },
          activeTintColor: colors.primary,
          inactiveTintColor: colors.gray,
        }}
      >
        <Tab.Screen
          name="Search"
          component={SearchScreen}
          options={{
            tabBarLabel: 'Pesquisa',
            tabBarIcon: ({ color }) => (
              <FontAwesome name="search" size={20} color={color} />
            ),
          }}
        />
        <Tab.Screen
          name="Favorites"
          component={FavoritesScreen}
          options={{
            tabBarLabel: 'Favoritos',
            tabBarIcon: ({ color }) => (
              <FontAwesome name="heart" size={20} color={color} />
            ),
          }}
        />
      </Tab.Navigator>
    </NavigationContainer>
  );
}

        
        import React from 'react';
import { View, StyleSheet } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';
import { colors } from '../constants';

export default function Header() {
  return (
    <View style={styles.container}>
      <FontAwesome name="github" size={32} color={colors.white} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    backgroundColor: colors.header,
    paddingVertical: 16,
    alignItems: 'center',
  },
});

        
        import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

export default function SearchScreen() {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Tela de Pesquisa</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
    alignItems: 'center',
    justifyContent: 'center',
  },
  text: {
    fontSize: 18,
    fontWeight: 'bold',
  },
});

        
        export const colors = {
  header: '#040404',
  TabBar: '#F5F5F5',
  background: '#FDFDFD',
  folder: '#7EB6FF',
  repositoryTitle: '#040404',
  repositoryDescription: '#D6D6D6',
  primary: '#000000',
  gray: '#808080',
  white: '#FFFFFF',
};
